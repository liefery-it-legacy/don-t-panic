## How to QA the courier app

* Get the android phone from Nik/Chris. There is no password.
* Ask them if they have a weird version installed. If they do, please do the following:
  * delete the app
  * go to the app store and download the "Liefery Kurier" app
  * open it
  * click 10 times on the Liefery logo to switch it to staging. You'll know it's staging because the bar at the top of the app will be RED instead of BLUE
* If you are testing new changes, update the app by going to the app store and updating to the new version
* Log in as a courier (if you don't already have/know of a courier account on staging, please make one)
* Create tour schedules/appointments with shipments and dispatch tours in the staging backend. Assign these tours to your courier. You will get a notification in the app & from there you can pick up the tour & drop off shipments.
* Make sure you occasionally go to the "Kontrollzentrum" and check the "Synchronisationsstatus" to make sure your deliveries etc.. have been synchronized. If not, follow the instructions to synchronize them.

It is also important to test the offline mode. Additional information can be found [in the courier app's own offline-mode 101 documentation](https://github.com/liefery/android-courier-app/blob/5c8af5ebf9e98a082c9678806e249e98ee66564a/docs/offline-mode.md).

### Workflow

The android team has it's own jira board which can be found [here](https://liefery.atlassian.net/secure/RapidBoard.jspa?rapidView=14&view=detail&selectedIssue=LIEF-9543)

Tickets which are in "Alpha Channel" are ready to be QA'd. Once they pass QA, you can move them into the Beta Channel and then the Accepted column.
