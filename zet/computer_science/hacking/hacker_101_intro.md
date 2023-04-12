# Welcome to Hacker 101! 

## Basics

* How to identify, exploit, and remediate the top web security
  vulnerabilities, as well as many more arcane bugs
* How to handle cryptography
* How to design and review applications from a security standpoint
* How to operate as a bug bounty hunter or security consultant

### Must-have tools

* Burp Proxy (free edition is fine)
  - This allows yo to watch all HTTP(S) communications, intercept and
    modify requests, and replay existing requests
  - You will use this constantly in both your coursework and exams
* Firefox
  - Firefox allows you to set proxy settings specifically in the
    browser, rather than setting them system-wide
  - This will be your friend when you're testing, to isolate an
    application. 

### Breaker Mindset

* You are here to break things before anyone else does, that that
  vulnerabilities can be fixed before attackers get to them.
* To do that, you need to understand how attacker operate, what their
  goals are, and how they think.
* The most important tenet of the breaker mindset is this: pushing a
  button is the most effective way to discover what it does.
* If you don't understand what an application is doing and why it's
  doing it, you're going to have a hard time finding ways to break it.
* The key difference between defending and attacking is this:
  - Defenders have to find every bug; attackers only need to find a few.
  - The means the attackers will always have the advantage over
    defenders.
* The primary consequence of this imbalance is that you will never find
  every buy, especially under time pressure.
* This means you have to prioritize to ensure that where there are still
  bugs, that impact will be relatively low.
* Making an accurate assessment of high-risk areas is critical.
* When assessing an application, find every bit of functionality you
  can.
* Once you have a rough list of all of the bits of the application,
  consider this:
  - If I was an attacker, what would my goal be?
* Maybe I'd want credit card numbers from an ecommerce site.
* Maybe I want to destroy of falsify data in a server monitoring
  application.
* Once you have a good picture of what an attacker might want, you can
  start to rank areas of the application in terms of payoff.
* If I compromise area X, does that give me low-value information or
  high-value? What about Y instead?
* When possible, asking developers the question "What keeps you up at
  night?" will often point to areas to check.

## Report

### Findings

For the purposes of this course, you should include the following for
each vulnerability:

* Title -- E.g. "Reflected Cross-Site Scripting in profiles"
* Severity
* Description -- Brief description of what the vulnerability is
* Reproduction Steps -- Brief description of how to reproduce the bug;
  preferably with a small proof of concept
* Impact -- What can be done with the vulnerability?
* Mitigation -- How is it fixed?
* Affected assets -- Generally a list of affected URLs.

### Severity

This is handled differently just about everywhere, but I recommend
basing severity on difficulty of exploitation and potential business
impact. The following rankings are what I use:

* Informational -- Issue has not real impact
* Low -- The business impact is minimal
* Medium -- Potential to cause harm to users, but not revealing data
* High -- Potential to reveal user data or aids in exploitation of other
  vulnerabilities
* Critical -- High risk of personal/confidential data exposure, general
  system compromise, and other severe impacts to the business.

## Bug Example

```php
<?php
if(isset($_GET['name'])) {
  echo "<h1>Hello {$_GET['name']}!</h1>";
}
?>
<form method="GET">
Enter your name: <input type="input" name="name"><br>
<input type="submit">
```

The code is pretty straightforward:

1. Was a 'name parameter passed via GET?
  * If so, print 'Hello <name>!' in an h1 tag
2. Print a form from the user to enter their name

With such basic code, what could really go wrong?

* You can inject a script into the name tag.
* Reflective cross site scripting.

