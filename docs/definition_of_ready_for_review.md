# Definition of Ready for Review

A checklist of things we should be aware of before making a pull request.
Is your code ready to be reviewed?

1. Did you run code linters?

2. If you added I18n keys, are they in alphabetical order?

3. Did you do basic QA yourself?
   * Did you click through the browser to make sure everything works?
   * Does the page still look nice?
   * If you added a Feature Flag, does the code work when it's turned on AND off?


4. Did you address all the requirements in the ticket?

5. Are all your code changes relevant to your ticket? Small drive by fixes are ok.

6. Did you write enough/the right kinds of specs?

7. Did you think about edge cases?
   * ex: If you added something to the view, should everyone be able to see it?
   * ex: Can you guarantee that every association you've accessed will be there at that time? (ex: vehicle stops)

8. Will your changes have a negative impact on performance?

9. Did you take screenshots of the changes to attach to your PR?

10. Have you anticipated reviewers' comments?
