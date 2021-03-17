# Interview Coding Challenges
 
## Address Normalisation

At Liefery we work a lot of with addresses.
Our customers use our Partner API to send us addresses of shipments that should be delivered.
These addresses often come from old enterprise systems and are a bit messy.
Our job is to bring these addresses into some standard form - normalise them.

Address coming from a decent / problematic customers may look like this and should all be normalized to the last version:
* `Emdener Str. 38, 10551, Berlin`
* `Emdener STR. 38, 10551, BERLIN`
* `Emdener Str. 38, 10551, Berlin, Deutschland`
--------------------------------
* `emdener str. 38, 10551, berlin`

Your task is to create a reusable piece of code responsible for address normalisation which we could later use
in our applications. It should accept an address coming from a customer and return the normalised address.
Please start by satisfying the basic requirements described below,
but be prepared for some follow-up changes to the requirements.

**Follow-up tasks:**
1. There are so many ways that people can write the street in German addresses! Straße, Strasse, or Str.
   For the maximum compatibility with other systems we would like to normalise it as strasse.
   This means that they should now be normalized like this:
   * `Emdener Str. 38, 10551, Berlin`
   * `Emdener Straße 38, 10551, Berlin`
   --------------------------------
   * `emdener strasse 38, 10551, berlin`

1. From time to time our customers experience technical difficulties with their systems and start sending us addresses
   without zip codes, for example `Emdener Str. 38, Berlin` or `Emdener Str. 38, Berlin, Berlin`.
   In such a case your reusable piece of code should reject an address and communicate it to the calling code.
   Please assume that zip codes in Germany consist of 5 digits.
1. We have one old customer that operates only in Berlin and therefore doesn't want to send us the city name.
   Instead they prefix the zip code with BER so the address may look like `Emdener Str. 38, BER10551`.
   Please handle this special case in your code.


## DNA Sequence
* Given a dna sequence `CAGT…`, return the complementary sequence (reversed)
* What about mixed case
* What if we change the rules or introduce new base pairs?
* Ignore unexpected characters
* Preserving white space

## Shipment Reference Code

In our system at Liefery we use reference codes for shipments. A reference code of a regular shipment looks like this:
`K7DTY-BXV6T` (five capital letters or digits, followed by a dash, followed by five more capital letters or numbers)

If we cannot deliver a shipment on a given day, we try delivering it another time in the coming days.
In that case we generate a new reference code, based on the old reference code.
For the second delivery attempt this would be: `K7DTY-BXV6T-2`
For a third one `K7DTY-BXV6T-3`
And so on.

Your task is to create a reusable piece of code that accepts a reference code of a shipment and prints the
reference code of the “original shipment”.
At the beginning you can assume that the provided reference code is always valid, but please be prepared for a few
follow-up tasks where this will change.
If the reference code of the “original shipment” is passed as an input, it should be printed unchanged.

**Follow-up tasks:**
1. `K7DTY-BXV6T-1` is invalid in our system, because we don’t append “-1” to the first delivery attempt.
   Modify your module to communicate this exceptional situation to the calling code.
2. Our colleagues from operations face a lot of problems with German keyboard layout.
   Instead of dash we receive ß. Modify your code to also accept input such as `K7DTYßBXV6Tß3` and 
   still return the correct result.
3. When the end receivers search for their shipments they can sometimes be really absent minded.
   They don’t pay too much attention to the upper case letters in the reference number.
   Modify your module to accept reference codes with lower case letters.
   The returned original reference code should still have upper case letters.
4. We have some legacy shipments in our system that use the following format as the reference code:

`K7DTY` (five capital letters or digits)
In that case a “repeated shipment” has a reference code that looks like follows: `K7DTY-2`
Modify your module to handle the legacy shipments as well.

## Take home task

Please treat the below task as if it were production code.
The task is intentionally kept relatively simple, but please treat it as and take the same care as if it would
be production code and a core part of our logic.
You don’t have to setup external services though, we’re just talking about the code.

In our system at Liefery we use reference codes for shipments. A reference code of a regular shipment looks like this:
`K7DTY-BXV6T` (five capital letters or digits, followed by a dash, followed by five more capital letters or numbers)

If we cannot deliver a shipment on a given day, we try delivering it another time in the coming days.
In that case we generate a new reference code, based on the old reference code.
For the second delivery attempt this would be: `K7DTY-BXV6T-2`
For a third one `K7DTY-BXV6T-3` 
And so on.

Your task is to create a module that accepts a reference code  of a shipment (for example “K7DTY-BXV6T-3”) and
prints the reference code of the “original shipment” (in our example: “K7DTY-BXV6T”) to the console.
You can work in any programming language you like and just to repeat, this doesn’t need to be a web app.
A simple CLI tool is fine. You don’t even need to get the reference code as input from the console,
we can just change it in the code.

You can assume the reference code to be generally correct except for the conditions outlined below:
* If the reference code of the “original shipment” is passed as an input, it should be printed unchanged.
* `K7DTY-BXV6T-1` is invalid in our system, because we don’t append “-1” to the first delivery attempt.
  Modify your module to communicate this exceptional situation to the calling code.
* Our colleagues from operations face a lot of problems with German keyboard layout.
  Instead of dash we receive ß. Modify your code to also accept input such as K7DTYßBXV6Tß3 and
  still return the correct result (original shipment reference code).
* When the receivers search for their shipments they can sometimes be really absent minded.
  They don’t pay too much attention to the upper case letters in the reference number.
  Modify your module to accept reference codes with lower case letters.
  The returned original reference code should still have upper case letters.
* We have some legacy shipments in our system that use the following format as the reference code:
  `K7DTY` (five capital letters or digits)
  In that case a “repeated shipment” has a reference code that looks like follows: `K7DTY-2`
  Modify your module to handle the legacy shipments as well.

And that is it!
We’ll look at the code together during the interview, possibly change it and we might also come up with new additions
to it but it will all be in this domain.
Again, please treat it as if it were production code and don’t hesitate to ask us if you have any questions.
