# Database

## Schema and data migrations

Our production database contains tables with a big enough number of records to
make certain database migrations dangerous for the stability of the system.
Some schema changes (for example, adding an index non-concurrently) can lock the
whole table for hundreds of seconds, preventing any updates to it in the
meantime.

This means that when writing database migrations we have to be more careful
than the default Rails approach, avoid antipatterns, and split changes into
multiple deployments if necessary.

For a general list of caveats not specific to our application, please, check
out [strong_migrations](https://github.com/ankane/strong_migrations/blob/master/README.md#dangerous-operations).

### Pre-restart and post-restart

Our migrations are divided into two categories: pre-restart migrations and
post-restart migrations. The order during a deployment is:
1. run pre-restart migrations. This should execute under 30 seconds
1. restart application (app server and background processes)
1. run post-restart migrations. This may take a longer time and executes in the
   background

### Changes that are safe

These changes should be added in a pre-restart migration.

1. creating a new table
1. adding a new column (with or without a default value - [see the correct way](#adding-a-column-with-or-without-a-default-value))
1. adding `NOT NULL` constraint to an existing column. Use `change_column_null`
   instead of `change_column`, because it produces a more efficient SQL statement.
1. adding a foreign key
1. removing an index with a uniqueness constraint
1. dropping a table

#### Adding a column with or without a default value

- Add the column in a pre-restart migration. If you're adding a `NOT NULL` constraint it can be defined in the same migration. This constraint is optional, but it is usually a good idea when you have a default value.

```ruby
class AddSomeColumnToShipments < ActiveRecord::Migration[5.2]
  phase :pre_restart

  def change
    add_column :shipments, :some_column, :text, default: "Nothing", null: false
  end
end
```

### Changes that are not allowed

1. adding an index using the standard way. See the
   [correct way](#adding-an-index-concurrently)

### All other changes

All other changes are considered "complex" and should be executed according
to the instructions below. If you're making a change that is not described in
this document, do not assume that it's "safe". Consult with the rest of the
team how to properly execute it, and document here.

### Changing data

Data migrations should be always introduced in a post-restart migration.
Please refer to [these guidelines](#guidelines-for-data-migrations) when
writing data migrations.

```ruby
class ChangeMyData < ActiveRecord::Migration[5.2]
  phase :post_restart

  class User < ActiveRecord::Base
  end

  def up
    commit_db_transaction

    User.where(name: nil).in_batches.update_all some_column: "default_value"
  end

  def down
  end
end
```

### Adding an index concurrently

Adding indexes is an expensive process that blocks other write statements for
the duration of the operation. Fortunately PostgreSQL provides `CONCURRENTLY`
option that makes the operation longer, but doesn't block other statements.

```ruby
class AddSomeIndexToUsers < ActiveRecord::Migration[5.2]
  phase :post_restart

  def up
    commit_db_transaction

    add_index :users, :some_index, algorithm: :concurrently
  end

  def down
    remove_index :users, :some_index, algorithm: :concurrently
  end
end
```

### Removing a non-unique index

1. add a post-restart migration to remove it concurrently

```ruby
class RemoveSomeIndexFromUsers < ActiveRecord::Migration[5.2]
  phase :post_restart

  def up
    remove_index :users, :some_index, algorithm: :concurrently
  end

  def down
    commit_db_transaction

    add_index :users, :some_index, algorithm: :concurrently
  end
end
```

### Removing a column

1. make sure the column is not used by any application code
  1. Otherwise first do a separate deploy to not use the column, because it's not so easy to rollback if changing the
     code to not use the column and removing the column happens in one deployment, because then we need to rollback
     the migration and fill the column with the correct values again.
1. ignore the column in the model
1. add a post-restart migration to remove it
1. add a ticket to remove the column name from ignored_columns in the following deployment

```ruby
class User < ApplicationRecord
  self.ignored_columns = ["unnecessary_column"]
end
```

```ruby
class RemoveUnnecessaryColumnFromUsers < ActiveRecord::Migration[5.2]
  phase :post_restart

  def change
    remove_column :users, :unnecessary_column, :boolean
  end
end
```

### Renaming a column / changing column type

1. add a new column with a new name (pre-restart migration)
1. change application code to write to both columns
1. populate the new column for old records (post-restart migration)
1. deploy
1. change application code to use the new column
1. deploy (first keep the old column, to be able to rollback in case the code change doesn't work)
1. [remove the old column](#removing-a-column)

Changing a column type without changing the name is even more involved, so just
pick a new name.

### Adding a money column

RubyMoney provides the [`add_monetize`](https://github.com/RubyMoney/money-rails/blob/master/lib/money-rails/active_record/migration_extensions/schema_statements_pg_rails4.rb#L5)
migration helper to simplify adding new
columns to databases. We do _not_ use it to reduce the chance of our migrations getting broken by a change in the gem.
Instead we create three separate migrations for creating the column, updating
existing rows, and adding the not null constraint. Here is an example of what
you want to end up with:

```
:table, "name_cents", :integer, null: false, default: 0
:table, "name_currency", :string, null: false, default: "EUR"
```

### Updating fulltext index

Changes to our fulltext index are typically done in 2 steps:

1. change the trigger that calculates the fulltext index (pre-restart)
1. touch all rows to invoke the trigger (post-restart)

Using the technique from above this can be done with the following migration:

```ruby
class UpdateFulltextIndex < ActiveRecord::Migration[5.2]
  phase :pre_restart

  def up
    execute <<-SQL
      CREATE OR REPLACE FUNCTION my_fulltext_index_update() RETURNS trigger AS $$
      ...
    SQL
  end

  def down
    execute <<-SQL
      CREATE OR REPLACE FUNCTION old_function_from_previous_migration() RETURNS trigger AS $$
      ...
    SQL
  end
end
```

```ruby
class TouchRecords < ActiveRecord::Migration[5.2]
  phase :post_restart

  class User < ActiveRecord::Base
  end

  def up
    commit_db_transaction

    User.in_batches.update_all(updated_at: Time.zone.now)
  end

  def down
  end
end
```

### Counter Caches

We have two ways of creating counter caches: the [in-built Rails standard](https://guides.rubyonrails.org/association_basics.html#options-for-belongs-to-counter-cache), and the [Counter Culture gem](https://github.com/magnusvk/counter_culture).

The vanilla Rails way should be preferred where possible, but for more complex counter caches - for example a conditional count of associations in a particular state, like "count a tour's non-canceled shipments" - we prefer to use the gem rather than add our own callbacks.

We want to avoid the situation that we introduce and begin using a counter cache, but the data migration to populate that counter cache column takes so long that our users notice incorrect values before it finishes.

This can be achieved by:
1. Populating more recent records first
    * This minimises the migration time for records that are likely to be seen by our users
    * See the documentation on data migrations for more details
1. Introducing the counter cache over 2 seperate deployments
    * Note this only works for caches created with the [Counter Culture gem](https://github.com/magnusvk/counter_culture)) 
    * See next point for a breakdown

Note that though Counter Culture provides methods for populating its caches, they are very slow and inefficient, so we prefer to populate the columns manually with the steps described in our data migrations section. 

#### Adding a Counter Cache over 2 deployments

First deployment: add and populate the new counter cache column (without actually using it in the code)
    * one migration to add the cache column
    * one data migration to populate the cache for existing records
    * add code to the model that updates the cache column
Second deployment: begin using the already-filled counter cache column in the application code
    * for example, change `shipment.comments.size` to `shipment.comments_count`

Doing it this way avoids the problem that a counter cache begins reading values before those values have been correctly populated.

Unlike Counter Culture's counter caches, Rails' in-built counter caches automatically recognise that an ActiveRecord query can be answered by the counter cache and do not need to be called explicitly. This means there is no avoiding the gap in time between deploying a cache and the cache being populated. Instead you should try to populate recent records first  - see data migration guidelines.

### Enabling/disabling PostgreSQL extensions

Switching extensions on and off requires superuser privileges, which our deployment user doesn't have,
so we cannot do it in a migration. Ask Makandra to do it for you before you deploy the code
that depends on the extension. And notify the team to do the changes locally.

### Migrations in development

Please use `rake db:migrate_dev` instead of the standard `rake db:migrate`.
It runs migrations in the proper order - split into pre-restart and
post-restart phases. Post-restart migrations are executed in the background
so you don't have to wait for the long running data migrations to finish.

If you're creating and testing a post-restart migration you can use
`RUN_IN_FOREGROUND=true rake db:migrate_dev` so the results are easily visible
to you.

### Reversible migrations

All migrations should be possible to revert. Please test this scenario on your
local machine by running `rake db:rollback` after you run `rake db:migrate_dev`.

### Small and focused migrations

Please, try to keep the migrations focused by only doing the changes that need to happen together.
Referencing too many tables, especially ones that are actively used in the app, in a single migration
may lead to a deadlock. Also, doing less work means less chances that a migration will fail, and if it does fail,
there would be less wasted effort.

### Guidelines for data migrations

Good practices when populating data in a migration:
1. batching
    * update/delete the records in batches, not all at once, to reduce memory consumption
    * 1000-10000 records per batch is a good amount for updating records when updating with `update_all` - if the table has a lot of indices (5+) generally use the lower side of the range
    * 100 records per batch is a good amount for updating records when they need to be loaded into ruby memory and saved individually (using `update` / `save`)
    * 10000-50000 records per batch is a good amount for deleting records 
    * *Take these numbers with a grain of salt, depending on the application/query that you are applying them to and follow your intuition to lower/raise them*
1. throttling
    * reduce the load by pausing briefly between batches
1. running the migration outside a transaction
    * Rails migrations run inside a transaction by default, so you'll need to commit the transaction
1. running the migration in the post-restart phase 
    * because they usually take longer 
1. create an empty version of whichever classes you reference in the migration 
    * classes come and go, but migrations are forever: we want to ensure the migration doesn't throw an `uninitialized constant` error if we later remove from the codebase the class that we're referencing.
1. keep it fast 
    * use `update_all` to send a single SQL query per batch
    * for populating especially large data sets, raw SQL can be preferable to ActiveRecord methods, because we don't need to load any objects into memory, and because it avoids N+1 queries
Here's a good example with comments pointing out the important aspects:
1. idempotent
    * you should assume that your data migration may fail at any point during its execution. In such a case it should be possible to restart the data migration without creating any unwanted duplicate records. What is more, the migration should not update the records that were already updated. Instead, it should pick up from the point where it failed.
1. feedback
  * a data migration should log its progress at regular intervals.
  * log to Kibana rather than using `puts`

```ruby
class PopulateTourAppointmentsSomeColumn < ActiveRecord::Migration[6.0]
  # 4. post-restart
  phase :post_restart

  # 5. Open an empty version of the class we're referencing
  class TourAppointment < ApplicationRecord; end

  def up
    # 3. Run the migration outside of a transactionn
    commit_db_transaction

    # 1. Batch using `in_batches` and 6. Keep it idempotent by only updating rows that contain the old value
    TourAppointment.where(some_column: "old_value").in_batches(of: 1000) do |appointments|
        # 6. speed - use update_all to minimise SQL queries
        appointments.update_all(some_column: "some_value")

        # 7. feedback
        Rails.logger.info(event_type: :my_migration, message: "progress")

        # 2. Throttle by pausing briefly between batches
        sleep 0.01
    end
  end
end
```

For futher reading, some of these practices were adapted from [here](https://github.com/ankane/strong_migrations).

## Locking order

In order to avoid deadlocks we have to keep the locking order consistent across
the whole codebase. This is important when locking multiple records one after
another. The general rule is "parent => child" - start by acquiring the lock on
the most high-level record you need and then acquire locks on its children.
For the models that we commonly lock it means:

1. `TourSchedule`
2. `TourAppointment`
3. `Tour`
4. `VehicleStop`
5. `Shipment`

In case you want to lock multiple records that don't have a common
parent at once, lock them in the order of their IDs, ascending.
