# QA

This page contains tips for QAing liefery-backend and Fulfillment.

Please check the appropriate docs for additional information on how to QA the [Label Printer](https://github.com/liefery/don-t-panic/blob/master/docs/how_to_qa_the_label_printer.md) and the [Courier App](https://github.com/liefery/don-t-panic/blob/master/docs/how_to_qa_the_courier_app.md).

## QA tips for liefery-backend

To QA the backend app it helps to have your own test data setup similar to this:

* use your own user
* make them a customer (with tour appointments, see further down) and a courier
  * this enables e.g. testing courier and customer invoices very easily
* use your own courier company for the courier role
  * like this there is no big list to choose the courier from when assigning the courier to a tour
* enable `allow_drop_of_in_safe_place` and `allow_drop_of_with_neigbor` and also check the `default` checkbox
  * like this it's always possible to test these 2 drop off outcomes without having to enable that for each shipment
* use your own tour appointment templates with it's own tour schedule
  * like this it's less likely that some of the other colleagues change settings while testing something
* set your courier company as the default for your tour appointment templates
  * like this you don't have to reassign the courier company for a shipment except for when creating a single shipment
    (which you can also already select during shipment creation)
* save addresses in the address book
  * that saves a lot of time since you only need to create 3-5 addresses manually which you can then reuse all the
    time by selecting them from the dropdown
* set the email address for the addresses and courier company to your own Liefery email address and enable all
  email flags except for the one that is sent to the OCC to not spam them
  * like this all the email types for customer, courier company, recipient and pick up contact can be tested at once
* now createa filter for all the staging emails to go to a separate folder in outlook and directly mark them as read
  to not disturb you. If you need to check an email you always know where to look for it.

## Fulfillment App

**How to create an order**

If you want to create an order via fulfillment app you neet to make sure to have a customer/Kunde and products for this customer in the fulfillment app.

Step by Step

1. Go to the fulfillment app https://fulfillment-staging.liefery.com/
2. Login to the fulfillment app
3. Go to “Kunde” (customer)
3. Klick on the blue button “Neuer Auftrag” (order)
4. Create the order by filling in all the necessary information
  * Tour appointment ID (can be found on the user page in the backend)
  * Recipient details
  * Package details
  * Add at least one product
  * Save the product by clicking “ok”

**How to pack an order**

If you want to pack an order you need to create one at first. Packing an order results in a shipment booking in our system.

Step by Step

1. Go to the fulfillment app https://fulfillment-staging.liefery.com/
2. Login to the fulfillment app
3. Go to “Aufträge”
4. Click on the “Einpacken” button for the needed order
5. Click on the “Einpacken” button again
6. You should see a confirmation page incl. The Liefery ID of the shipment in the backend
