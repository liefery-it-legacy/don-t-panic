# Ruby / Rails styleguide

## Architecture / code structure (where to put which code)

These are general guidelines we want to follow, especially for big code bases.
However, if e.g. starting a new Ruby project, it doesn't mean that we have to use all of the patterns described below.
If we e.g. don't have big SQL queries, we don't have to use query objects.


### Useful resources

Here are some helpful links which describe why Rails MVC is not enough and why we need more architectural elements:
* https://railshurts.com/
* https://leanpub.com/trailblazer
* https://leanpub.com/growing-rails


### Recorded meeting
There is a [recorded meeting](https://zoom.us/rec/share/ptd1cu3axjJITI2XtRraS6cvF7-maaa80ClN8vZfxEhXVzzaEWRm1-_MzQbmLAtq) in which the following guidelines where explained and discussed.


### Decorator

A decorator should add additional information and helper methods for a model.
These are typically used for the view, e.g.

* Formatting, e.g. I18n.l
* Methods wrapping attributes in a little bit of markup, e.g. links

* No complete parts of the UI, e.g. like [here](https://github.com/liefery/liefery-backend/tree/0f9c201f245b68898f501ba3fb4989462f441f4e/app/decorators/shipment_decorator.rb#L18)

Suggestion:

Move methods defining part of the UI structure into view partials


### View

The view partials should mostly describe the structure of the view.
Please avoid adding a lot of logic directly in the view.
This logic should go into the [ViewModel class](#View model).


### View Model

Here goes the logic that is necessary for the view.
[Here](https://github.com/liefery/liefery-backend/pull/7572/files#diff-554752acd124b4ec52b06435ce955a37R4)
is one example for this.


### Form object

A form object encapsulates the logic for updating and rendering the necessary attributes for one use case
This enables flexibility regarding whitelisting editable attributes and conditional validations without the need
of using a lot of if/else cases in the model for when to execute the validations.

Question: how to deal with global rules so that all the form objects make sure one value is present -> DB index?


### Model

The model should only contain methods describing its state and relations and for querying the DB.
This includes:

* associations
* scopes
* small helper methods describing the model's state, e.g. sth. like
```ruby
def editable?
  new? && unused?
end

def invoice_review_required?
invoice_review_note.present?
end

def courier_with_home_location?
courier? && courier_home_location.present?
end
```

* no validations
* no business code, usually methods containing/starting with a verb, which indicates a process rather than a state
  * `calculate_*`
  * `send_notification`
  * `update_*`

* state machine: is also business code, should be done by service objects
* no callbacks (usually business code, and therefore part of service object) unless there is no other way


### Controller

Controlling what to do and what to respond.

* which service should be called
* HTTP things
* respond with success / failure message

Note: much of our controller logic is generated "under the hood" by ActiveAdmin. We can manually configure this logic in the page definition files located in `app/admin`. But these files are more than just controllers: they can also contain view logic and other stuff that doesn't belong in a traditional Rails controller.

Controllers should not contain any business logic or model-related information, e.g. [here](https://github.com/liefery/liefery-backend/tree/0f9c201f245b68898f501ba3fb4989462f441f4e/app/controllers/api/partner/v2/tour_shipments_controller.rb#L15)
https://github.com/liefery/liefery-backend/tree/0f9c201f245b68898f501ba3fb4989462f441f4e/app/admin/tour_schedule_template.rb#L106-L108


### Service object

Here goes the business logic. The business logic describes the processes that happen in the application, so everything that changes things.
This includes:

* create/edit models
* send mails
* change states, …
* cron jobs happening in the background


### Query Objects

Big big queries can be encapsulated by query objects to improve readability and reduce the query logic in models.
[Here](https://github.com/liefery/liefery-backend/blob/0f9c201f245b68898f501ba3fb4989462f441f4e/app/queries/find_shipments_for_tour_appointment_volume_calculation.rb) is one example.

Question: What should be the returned type? AR::Relation or arrays or ..?


### Transformers

We currently still have the `app/transformers` folder but since the concept is quite vague and they are basically [POROs](https://myflatironexperience.wordpress.com/2015/04/21/poro-plain-old-ruby-object/) we want to get rid of this separate "type" of objects.


## General

* Generally follow the [Ruby style guide](https://github.com/bbatsov/ruby-style-guide) - also enforced via RuboCop
  * Prefer `public_send` over `send` when needed
  * Use brackets `()` in method definitions `def`
* Avoid **unnecessary comments**: Better write better code than comment bad code. A comment is an excuse of the code that it could not be cleaner. If you comment, describe the WHY not the WHAT. E.g. why is this business logic like this. Exceptions are (for instance) our top level class module/docs to be able to get into the code better.
* Align code using whitespace where it improves the readability (not enforced by
  RuboCop):

  ```ruby
  # passable

  tour_appointment = shipment.tour_appointment
  tour_schedule = shipment.tour_schedule

  # better

  tour_appointment = shipment.tour_appointment
  tour_schedule    = shipment.tour_schedule
  ```
* We should only stub time (using TimeCop) when we have no way to manually set the value (set created_at or other timestamp attribute) or to inject the value (make the method receive a time argument that has a default value):

  * Time can be injected
  ```ruby
  # bad

  ## code
  def set_time
    self.time = Date.current
  end

  ## test
  Timecop.travel Date.yesterday do
    object.set_time
  end

  # good

  ## code
  def set_time(new_time = Date.current)
    self.time = new_time
  end

  ## test
  object.set_time(Date.yesterday)
  ```

  * Time can be manualy set
  ```ruby
  # bad

  ## test
  Timecop.travel Time.current - 1.day do
    create :shipment
  end

  # good

  ## test
  create :shipment, created_at: Time.current - 1.day
  ```

## Models

* Use `SoftDeletable`- trait for models that need to be persisted after deletion

### Model structuring

Structure can save some time when searching for something and makes it easier to get an overview about the content of the model/class.
Therefore please keep the model structure according to order below. Most of the things are also enforced with rubocop
by the [Layout/ClassStructure cop](https://rubocop.readthedocs.io/en/latest/cops_layout/#layoutclassstructure).


```ruby
class MyModelStructure < ActiveRecord::Base
  # includes/extends
  # constants
  # relations
  # scopes
  # validations
  # (hopefully not so many) callbacks
  # accessors
  # delegations
  # (non rails) class method calls (DSLs etc.)
  # class method definitions
  # instance method definitions
  # (protected) instance method definitions
  # private instance method definitions
end

class MyModel < ActiveRecord::Base
  include Module1
  extend Module2

  CONSTANT1 = "foo"

  belongs_to :owner
  has_one :single_record
  has_many :other_records

  scope :find_me, -> {}

  validate :attribute
  validates :some_method

  before_validation :dont_call_me_maybe

  accepts_nested_attributes_for :nothing

  attr_accessor :my_attribute
  delegate :method, to: :object

  # class method calls
  self.ignored_columns = %w(ignorant_column)

  event_log_ignore_attributes("foo")
  devise :database

  def self.execute
    # class method
  end

  def foo
    # instance method
  end

  private

  def not_for_everyone
    # private methods
  end
end
```


## Controller

* We use [consul](https://github.com/makandra/consul) as an authorization mechanism
  * Powers should always return scopes if possible (return `Model.none` instead of nil if forbidden)
  * Avoid creating powers that end with `?`, as this breaks some of consuls features
* APIs should be documented with [swagger](https://github.com/richhollis/swagger-docs)
* Use [serializers](https://github.com/rails-api/active_model_serializers) to render JSON data
  * directly reference Serializers by their class name when serializing associations (`has_one :some, serializer: SomeSerializer`, `has_many ...`)
* Use Background workers (sidekiq) for long-running tasks
  * Write [transactional and idempotent jobs](https://github.com/mperham/sidekiq/wiki/Best-Practices)
* Do not use `save` if you do not check the return value. Use `save!` if you don't want to check, since this raises an exception

## Views

* Use `arb` templates for the simple stuff and `haml` for the more complex views (pending: is arb slower than haml?)
* Prefer decorators over helpers
  * We use [Draper](https://github.com/drapergem/draper) for decorators in Rails
