## How to QA the label printer

To QA the label printer you need a windows computer, the label printer itself, and lots of patience.

* Turn on the label printer (!!)
* Turn on the windows computer
* The admin user's password is in 1Password
* It will take forever to boot up. Just be patient.

* If the changes have been made to the label printer itself, you will need to download a new version. You can do this as follows:
  * open the desktop folder and delete any instance of the label printer in there because we want to download the newest version & it just gets confusing if there's a bunch of them in there.
  * go to downloads-staging.liefery.com/labelprinter/_whatever-version-we-are-testing_ (ask a fellow dev what version you should be testing) E.g.: downloads-staging.liefery.com/labelprinter/v3.2.0/
  * click the download button
  * find the downloaded file in the downloads folder and double click it
  * copy the folder to the desktops folder (it will unpack it)
  * open the folder
  * double click the label printer app to open it (it's the one with the Liefery logo)
  * the app should now open and you can print a test label by clicking the button to do so

* If the changes have been made to the liefery backend, you don't need to do the above steps, but you still need to navigate into the label printer folder & open the app
  * print a test label to make sure everything is working

* go to staging.liefery.com/parcel_hub/scan and test your labels! Josi's user should already be saved, so you shouldn't have to log in as anyone strange.

### Troubleshooting

* If things aren't working, check the javascript console for errors. Maybe you found a bug!
* Print a test label to make sure things are hooked up correctly
