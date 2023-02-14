# Let's Talk About Forms

> Do forms scare you?
>
> The confuse me a bit so I would like to de-mistify them a bit today
while we look into how to write accessible forms.

## Some basic tips on writing HTML forms that are web accessible

1. Use descriptive labels: Provide labels that clearly and concisely
   describe each input field. Use the `for` attribute to associate the
   label with its corresponding input field.
2. Use the correct input types: Use the appropriate `type` attribute for
   each input field. Fir example, use `type="text"` for single-line text
   inputs, `type="email"` for email address inputs, `type="tel"` for
   telephone number inputs, and so on.
3. Provide instructions and hints: Use the `title` attribute to provide
   additional instructions or hints for each input field. You can also
   use the `placeholder` attribute to provide a short hint that appears
   in the input field itself.
4. Use autocomplete: Use the `autocomplete` attribute to allow users to
   easily fill in form fields based on their browser history or saved
   information.
5. Ensure form validation is accessible: Make sure that any validation
   messages are clearly visible and associated with the relevant input
   field. Use ARIA attributes if necessary to make the message clear to
   assistive technology users.
6. Text with assistive technology: Test your form with a screen reader
   or other assistive technology to ensure it is fully accessible.

Remember that web accessibility is an ongoing process, and it's
important to continually review and update your forms to ensure that
they remain complaint with the latest accessibility guidelines.

## What is the WCAG 2

The Web Content Accessibility Guidelines (WCAG) 2.0 is a set of
guidelines developed by the World Wide Web Consortium (W3C) that
provides a framework for making web content more accessible to people
with disabilities. Here are some key things to know about WCAG 2.0:

1. `Levels of Confromance`: WCAG 2.0 is divided into three levels of
   conformance: `A`, `AA`, and `AAA`. Each level has increasingly stringent
   requirements for accessibility. It's recommended that websites
   conform to as least the AA level, as this is considered the minimum
   level of accessibility that should be aimed for.
2. `Guidelines and Success Criteria`: WCAG 2.0 is organized into four main
   principles: `perceivable`, `operable`, `understandable`, and `robust`. Each
   principle contains guidelines, and each guideline contains specific
   success criteria that define what is required to meet the guideline.
3. `Testing Techniques`: WCAG 2.0 also includes a set of testing
   techniques that can be used to evaluate whether a website meets the
   success criteria. These techniques cover a range of topics, such as
   `color contrast`, `keyboard accessibility`, and `text alternative for
   images`.
4. `Compatibility with Additive Technologies`: One of the key aims of
   WCAG 2.0 is to ensure compatibility with assistive technologies such
   as `screen readers`, `magnifiers`, and `voice recognition software`. WCAG 2
   includes guidelines and success criteria that aim to make web content
   more compatible with these technologies.
5. `Ongoing Process`: Achieving WCAG 2.0 compliance is an ongoing
   process, and websites should be regularly reviewed and updated to
   ensure that they remain accessible. It's also important to note that
   while WCAG 2 is a widely recognized standard, it's not the only set
   of guidelines that websites may need to adhere to in order to ensure
   accessibility for all users.

## What are these other guidelines we mentioned?

In addition to WCAG 2.0, there are other guidelines and standards that
websites may need to adhere to in order to ensure accessibility for all
users. Some examples include:

* `Section 508`: This is a set of accessibility standards that applies
  to US federal agencies. Section 408 requires federal agencies to
  ensure that their electronic and information technology is accessible
  to people with disabilities.
* `Americans with Disabilities Act (ADA)`: The ADA is a US law that
  prohibits discrimination against people with disabilities in `various
  areas for public life`, including `employment`, `transportation`, and `public
  accommodations`. The ADA applies to websites that are considered `places
  of public accommodation`, such as `e-commerce sites`.
* `Accessible Rich Internet Applications (ARIA):` ARIA is a set of
  attributes that can be added to HTML to make web content more
  accessible to people with disabilities. ARIA is particularly useful for
  making web applications more accessible, such as dynamic content that
  updates without refreshing the page.
* `Web Content Accessibility Guidelines (WCAG) 2.1`: This is an updated
  version of WCAG 2.0 that was released in 2018. WCAG 2.1 includes
  additional success criteria that aim to improve accessibility for
  people with cognitive and learning disabilities, as well as people
  with low vision.

It's important to note that these guidelines and standards can vary
depending on the country or region, and there may be other specific
requirements that websites need to meed in order to ensure
accessibility.

## Let's get to some code


