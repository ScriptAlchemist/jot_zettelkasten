# Let's Talk About Forms

[WCAG 2.1](https://www.w3.org/WAI/WCAG21/Understanding/conformance#levels)

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

### Login Form

```html
<form>
  <label for="username">Username</label>
  <input
    type="text"
    id="username"
    required
    aria-describedby="username-desc"
  />
  <div id="username-desc">Please enter your username.</div>

  <label for="password">Password</label>
  <input
    type="password"
    id="password"
    required
    aria-describedby="password-desc"
  />
  <div id="password-desc">Please enter your password.</div>
  <button type="submit">Sign In</button>
</form>

<a href="#" aria-label="Forget your password?">Forget your password?</a>
```

#### What's happening in this example?

* Each for field has a label that is associated with the input using the
  `for` attribute. This ensures that the label is read aloud by screen
  readers when the input receives focus.
* The `aria-describedby` attribute is used to associate a descriptive
  message with each input field. This message provides additional
  information or instructions for the user and is read aloud by the
  screen readers when the input receives focus.
* The `required` attribute is used to indicate that both the username
  and password fields are required to be filled in.
* The `button` element is used to create the "Sign In" button, which is
  associated with the form using the `type="submit"` attribute.
* The "Forget your password?" link is provided as an anchor element
  (`<a>`) with and `aria-label` attribute to make it accessible to users
  who cannot see the text on the link.

This example is just one way to make a login and forget password button
for accessible. It's important to keep in mind that accessibility is an
ongoing process, and there may be additional steps you can take to make
your forms more accessible.

### That can't be all that we need to worry about

It's not there are several additional steps you can take to make your
login and forget password forms even more accessible. Here are some
examples:

* `Use ARIA roles`: ARIA roles can help to provide additional information
  about the purpose and functionality of form elements. For example, you
  can use the `role="button"` attribute on the "Forgot your password?"
  link to indicate that it behaves like a button.
* `Use proper focus management`: Proper focus management is important for
  ensuring that users can navigate your form using the keyboard. This
  means that focus should be moved to the appropriate element when the
  user interacts with the form, and that the order of the focus should
  follow a logical sequence.
* `Provide clear error messages`: When a user enters incorrect information
  in a form field, it's important to provide a clear and descriptive
  error message that explains what went wrong and how to fix it. The
  error message should be associated with the form field using the
  `aria-describeby` attribute, and should be read aloud by screen
  readers.
* `Provide visual feedback`: Users with visual impairments may not be
  able to see changes in the visual appearances of a form field when it
  receives focus. Providing additional visual feedback, such as
  highlighting the focusing element or changing its border, can help to
  ensure the user can see the changes when they interact with the form.
* `Text with assistive technology`: Finally, it's important to test your
  forms using assistive technology such as screen readers, magnifiers,
  and voice recognition software. This can help you to identify any
  issues that may be preventing users with disabilities from accessing
  and interacting with your forms.

## Full Blown Example

I want to build a large form using as many of the elements. Making is
basic accessible.

* `<button>`
* `<datalist>`
* `<fieldset>`
* `<input>`
* `<label>`
* `<legend>`
* `<meter>`
* `<optgroup>`
* `<option>`
* `<progress>`
* `<select>`
* `<textarea>`

```html
<form>
  <fieldset>
    <legend>Personal Information</legend>
    <label for="name">Name:</label>
    <input type="text" id="name" name="name" required aria-required="true">
    <br>
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required aria-required="true">
    <br>
    <label for="phone">Phone:</label>
    <input type="tel" id="phone" name="phone" required aria-required="true">
    <br>
    <label for="birthdate">Birthdate:</label>
    <input type="date" id="birthdate" name="birthdate" required aria-required="true>
  </fieldset>
  <br>
  <fieldset>
    <legend>Account Information</legend>
    <label for="username">Username:</label>
    <input type="text" id="username" name="username" required aria-required="true">
    <br>
    <label for="password">Password:</label>
    <input type="password" id="password" name="password" required aria-required="true" minlength="8">
    <br>
    <label for="confirm-password">Confirm Password:</label>
    <input type="confirm-password" id="confirm-password" name="confirm-password" required aria-required="true" minlength="8">
    <br>
    <label for="security-answer">Security Answer:</label>
    <input type="text" id="security-answer" name="security-answer" required aria-required="true">
    <br>
    <label for=remember-me">Remember Me:</label>
    <input type="checkbox" id="remember-me" name="remember-me">
    <br>
    <label for="newsletter">Subscribe to Newsletter:</label>
    <input type="checkbox" id="newsletter" name="newsletter">
    <br>
    <label for="plan">Select a Plan:</label>
    <select id="plan" name="plan" required aria-required="true">
      <optgroup label="Basic>
        <option value="basic-monthly">Monthly ($9.99)</option>
        <option value="basic-annual">Annual ($99.99)</option>
      </optgroup>
      <optgroup label="Premium">
        <option value="premium-monthly">Monthly ($19.99)</option>
        <option value="premium-annual">Annual ($199.99)</option>
      </optgroup>
    </select>
    <br>
    <label for="message">Message:</label>
    <textarea id="message" name="message" rows="4" cols="50"></textarea>
    <br>
    <label for="color">Favorite Color:</label>
    <input type="color" id="color" name="color">
    <br>
    <label for="datalist">Select a City:</label>
    <input type="text" list="cities" name="city" id="datalist">
    <datalist id="cities">
      <option value="New York">
      <option value="Los Angeles">
      <option value="Chicago">
      <option value="Houston">
      <option value="Phoenix">
    </datalist>
  </fieldset>
  <br>
  <label for="progress">Progress:</label>
    <progress id="progress" value="50" max="100"></progress>
    <meter value="5" min="0" max="10">5 out of 10</meter>
  <button type="submit">Submit</button>
</form>
```

In this example it includes appropriate labeling and identification of
form elements using the `label` and `for` attributes, as well as using
the `fieldset` and `legend` elements to group related form controls
together.

It also includes appropriate use of the `required` attribute and ARIA
attributes such as `aria-required` to indicate which form controls are
required, as well as the `list` attribute and `datalist` element for
providing suggestions for form input.

However, there are additional accessibility considerations that may need
to be taken into account based on the specific needs of the users and
the context of the application, such as using appropriate color
contrast, providing alternative text for images, and ensuring that form
controls can be operated with the keyboard.


