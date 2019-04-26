# CSS and Enhancing Web Accessibility

## What is Accessibility

> Web accessibility means that websites, tools, and technologies are designed and developed so that people with disabilities can use them.
> -- _[W3C](https://www.w3.org/WAI/fundamentals/accessibility-intro/)_

> Web accessibility means that websites, tools, and technologies are designed and developed so that people ~with disabilities~ *like and unlike yourself* can use them.

FTFY

When we devlop we we code not only with of our abilities and inabilities, but with the biasses of your experience, likes and dislikes

* Infrastructure (2G connection and 1GB internet)
* Financial means
* Visiual capabilites and availability 
* Language
* Auditory availability
* Dexterity (presence of keyboard or mouse)

_Accounting for these differences makes the web accessible._

## Why is it important

> The power of the Web is in its universality. Access by everyone regardless of disability is an essential aspect.
>  -- __Tim Berners-Lee__, W3C Director and inventor of the web

Alienate potential users, and generate a self-selecting client base

## Approach

This document will begin with pointed examples of HTML and speak of how CSS can help enhance vanilla HTML.

Leading will be exmaples at a more atomic level, and build towards a more wholistic view of a web page or web app.

Biases:
* Explicit over implicit
* Legibility over cleverness
* Simplicity is beautiful (and has fewer bugs)

## Enhancing buttons

### Form Buttons

Buttons have an under-utilzed `type` attribute.  It can be used to fulfill all kinds of style needs within a form.

A stylesheet should encourage symantic markup and seek to minimize complexity needed to obtain proper look and functionality.

```css
form button[type="submit"],
form button:not([type]) {
    // eye grabbing styles here
}
  
form button[type="button"] {
    // your typical styles here
}
  
form button[type="reset"] {
    // refreshing styles here
}
```

The above encourages proper syntax without added markup.  The `type` (and therefore functionality) of a `<button>` is now bound to the look.  A consistent experience is granted to sighted and non-sighted users.

The difference is quite pleasing:

```html
<button class="btn btn-form btn-danger">Reset</button>
<!-- vs. -->
<button type="reset">Reset</button>
```


Equally valuable could be a style to go a bit further to discourage lazy markup

```css
button:not([type]) {
    transform: scale(5);
}
```

### Toggle Buttons

[What is ARIA?](https://www.w3.org/WAI/standards-guidelines/aria/)

```html
<button aria-pressed="false" type="button" onclick="document.body.classList.toggle('dark')">
  <span>Use dark theme</span>
  <img src="moon.png" alt="🌙" />
  <img src="sun.png" alt="☀️" aria-hidden="true" />
</button>
```

Theme Example: [Smashing Magazine](https://www.smashingmagazine.com/)
Font Example: [Advanced React](https://advancedreact.com/)

In [ChromeVox](https://chrome.google.com/webstore/detail/chromevox/kgejglhpjiefppelpmljglcjbhoiplfn) this reads:

> Use dark theme cresent moon. Button not pressed.

```css
[aria-hidden="true"] {
  visibility: hidden;
}
button[type="button"][aria-pressed] {
  // toggle styles here
}
button[type="button"][aria-pressed="false"] {
  // unpressed styles here
}
button[type="button"][aria-pressed="true"] {
  // pressed styles here
}
```

### Popup Buttons
It may be the case to use a button to open or close a drop-down or to act as a toggle.

```css
button[type="button"][aria-haspopup="true"][aria-expanded="true"] {
  // active/open styles here
}

button[type="button"][aria-haspopup="true"][aria-expanded="false"] {
  // inactive/closed styles here
}
```

These styles can be used in lieu of classes.  This reduces visual clutter and the chance your JavaScript logic or applicaiton state is out of sync with your styling.

[Demo](https://codepen.io/Mathdrquinn/pen/vMVLMp)

## Enhancing Links

Style links based off `href` shape - screen readers declare this why shouldn't all get this benefit?
  
* External Links
  
    ```css
      a:not([href*="wikipedia.org"]) { /* your styles here */ }
    ```

    [Wikipedia example](https://en.wikipedia.org/wiki/Camino_de_Santiago#External_links)

* Local links
  
    ```css
    a[href^="/"],
    a[href*="your-site.com"]) {
      color: var(--self-serving-blue)
    }
    ```

* Internal links

    ```css
    a[href^="#"]:focus,
    a[href^="#"]:hover { // show neat copy link icon }
    ```

* Warn unsecured websites
  
    ```css
    a[href^="http://"] { color: var(--security-threat-red) }
    ```
  
* Secured sites

    ```css
    a[href^="https://"] { color: var(--comfort-blanket-gray) }
    ```
* Indicate file type

    ```css
    a[href$=".pdf"] { color: var(--adobe-acro-black) }
    ```

## Enhancing Headers

Targeted links and internal links are extremely important in a web looking for quick and percise answers. To aid the type of usage, all headers should have `id` attributes and quick ways to link to them.

A couple approaches:

1. Good Header

    ```html
    <h2 id="olive-trivia">How many bottles of olive oil does one olive tree make?</h2>
    ```

    * If you know the id, it can be linked to...but how would a user find and use this?

2. Better (1 of 2)

    ```html
    <h2 id="olive-trivia">
      <a href="#olive-trivia">
        <img src="neat-link-img.jpg" alt="Link to" style="width: 0.8em" aria-labelledby="olive-trivia">
      </a>
      How many bottles of olive oil does one olive tree make?
    </h2>
    ```

    * Perfect choice to enhance existing code with a small JavaScript function.
    * Works well with dynamicly generated HTML

3. Better Header (2 of 2):

    ```html
    <h2 id="olive-trivia">
      <a href="#olive-trivia">How many bottles of olive oil does one olive tree make?</a>
    </h2>
    ```
      
    * This reads much cleaner.
    * Very explicit to all that this is a link


Combine the above examples with the `:target` pseudo class and improve legibility.

```css
main [id]:target {
  outline: #66D9EF auto 0.125em;
}
```

This can be extremely beneficial when:
* A page has a fixed header that covers the top of the page, blocking the targeted content
* A page is too short to scroll to the targeted content
* Giving a point of reference when a user re-reads the page

## Enhancing Languages

I've often read a book or article not knowing if a particular work was simple unknown to me, or borrwed from a different language.

```css
html:not([lang]) > * {
    // punish them
    color: lightpink!important;
}
```

Target nested languages and signify the linguisic shift.

```css
[lang^="en"] [lang]:not([lang^="en"]) {
    font-style: italics;
    font-weight: lighter;
}
```

## Enhancing Forms

### Labels

Labels have the fascinating feature that clicking on them acts as a click upon their associated input,
yet browsers don't default to a style that conveys this functionality.

```css
label { cursor: pointer }
```

Better yet, clearify the association with the awesome (but not universal) `:focus-within`.

```css
label:focus-within {
    background-color: var(--aLabelster)
}
```

### Fieldsets

> The `fieldset` element represents a set of form controls optionally grouped under a common name.

> The `disabled` attribute, when specified, causes all the form control descendants of the `fieldset` element, excluding those that are descendants of the `fieldset` element's first legend element child, if any, to be disabled.
[source](https://www.w3.org/TR/2011/WD-html5-author-20110705/the-fieldset-element.html)

This lesser-known feature makes it easy to style and enable/disable secitons of a form.

```html
<style>
  @keyframes loading {
    from { background-position: 0 0; }
    to { background-position: 100% 100%; }
  }

  form {
    padding: 1em;
    max-width: 800px;
    margin: 0 auto;
  }
  form::before {
    height: 1rem;
    content: '';
    display: block;
    /* Credit for this trick goes to Wes Bos - https://advancedreact.com/ */
    background: linear-gradient(to right, #C564DB 0%, #002B54 50%, #C564DB 100%);
    margin: 0 0 1rem;
  }
  form[aria-busy="true"] > * { opacity: 0.5 }
  form[aria-busy="true"]::before {
    background-size: 50% auto;
    animation: 0.75s loading linear infinite;
  }

  fieldset { margin: 2em 0; }
  fieldset[disabled] > *:not(legend) { opacity: 0.5 }

  label { padding: 0.5em; display: block; }
  fieldset:not([disabled]) label { cursor: pointer; }
  fieldset:not([disabled]) legend label:hover,
  fieldset[disabled] legend label:hover { background-color: whitesmoke; }
  fieldset:not([disabled]) label:hover { background-color: whitesmoke; }
  fieldset label:focus-within { background-color: lightgray; }
  fieldset[disabled] legend label:focus-within { background-color: lightgray; }
</style>

<form onsubmit="this.user.disabled = true; this.emails.disabled = true; this.setAttribute('aria-busy', true); event.preventDefault();">
  <h2>Sign Up!</h2>
  <fieldset name="user">
    <legend>Your information</legend>
    <label>Your name: <input type="text" name="name"></label>
    <label>Your email address: <input type="email" name="email"></label>
  </fieldset>
  <fieldset name="emails" disabled>
    <legend>
      <label>
        <input type="checkbox" name="club"
            onchange="form.emails.disabled = !checked">
        Subscribe to additional emails
      </label>
    </legend>
    <label><input name="newsletters" type="checkbox"> I want to receive weekly newsletters</label>
    <label><input name="prodcut-updates" type="checkbox"> I want to receive new product updates</label>
    <label><input name="spam" type="checkbox"> I want to be promotional emails</label>
  </fieldset>
  <button>Submit</button>
</form>
```
[Demo](https://codepen.io/Mathdrquinn/pen/jRxXZW)

## Enhancing the Layout

There are many roles that can be used to convey important structural information about the page.  [Many of those are already associated with HTML5 elements.](https://www.w3.org/TR/2017/NOTE-wai-aria-practices-1.1-20171214/examples/landmarks/HTML5.html)

By attaching CSS styling via these tags and their placement in the document tree, the developer is encouraged to maintain proper stucture and flow to a page -  building a page that works best for the reader, not what what is most convenient to ship quickly.

Placement of markup has more value now.

```html
<body>
  <header>
    <!-- -->
    <nav aria-label="Site"></nav>
  </header>

  <main>
    <nav aria-label="table of contents"></nav>
  </main>

  <form role="search">
    <label>
      Search for site content:
      <input type="search" name="search" />
    </label>
    <button type="submit">Submit</button>
  </form>

  <aside id="profile" aria-labelledby="user-heading">
    <h2 id="user-heading">Your Profile</h2>
  </aside>

  <aside id="author" aria-labelledby="author-heading">
    <h2 id="author-heading">About the Author</h2>
  </aside>

  <footer>
    <nav aria-label="legal stuff"></nav>
  </footer>
</body>
```

Combine the accessible HTML with some CSS sugar to make for a quick layour

```css
body {
  display: grid;
  grid-template-columns: 150px auto minmax(200px, 25%);
  grid-template-rows: repeat(4, auto);
  grid-gap: 1rem;
  grid-template-areas:
    "banner  banner banner"
    "profile main   search"
    ".       main   author"
    "footer  footer footer";
}

header { grid-area: banner; }
main { grid-area: main; }
[role="search"] { grid-area: search; }
/* and more... */
```

[Demo](https://codepen.io/Mathdrquinn/pen/qwMdgQ)

### Enhancing Navigation with a Skip Nav

While landmark roles are great for screen readers, some users may be keyboard power users by choice or necessity and not have access to quick navigation across landmarks.

Some sites are adding a keyboard accessible "skip" nav to allow keyboard users the ability to jump to content.
    * [w3.org](https://www.w3.org/TR/wai-aria/)
    * [MDN](https://developer.mozilla.org/en-US/)
    * [Smashing Magazine](https://www.smashingmagazine.com/articles/)

How they deliver:

```html
<style>
  a.skip {
    left: -999px;
    position: absolute;
    top: auto;
    width: 1px;
    height: 1px;
    overflow: hidden;
    z-index: -999;
  }

  a.skip:hover {
    left: auto;
    top: auto;
    width: auto;
    height: auto;
    overflow: auto;
    padding: .5em 1em;
    text-align: center;
    font-size: 1.2em;
    z-index: 999;
  }
</style>
<nav aria-label="Quick">
  <ul>
    <li><a class="skip" href="#main">Skip to main content</a></li>
    <li><a class="skip" href="#toc">Skip to table of contents</a></li>
    <li><a class="skip" href="#search">Jump to Search</a></li>
    <li><a class="skip" href="/list">Go to list of articles</a></li>
  </ul>
</nav>
```

## Enhancing Font

> The key to inclusive design isn’t to target specific groups, it’s to not exclude groups arbitrarily — there’s nothing to gain.
> -- Heydon Pickering **Inclusive Design Patterns**

```css
html {
  font-size: 100%; // 100% of browser default, defines the size of 1rem;
  // font-size: 14px; // Bad.
}
```

This allows the rest of your document to scale with the user's preference.

Use `rem` and `em` for sectioning content and flow content, respectively.

```css
body > header, [role="banner"] { font-size: 1.5rem; }
main, [role="main"] { font-size: 1.25rem; }
aside, [role="complementary"] { font-size: 0.8rem; }
body > footer, [role="contentinfo"] { font-size: 0.75rem; }
```

```css
h1 { font-size: 3em; }
h2 { font-size: 2.25em; }
h3 { font-size: 2em; }

small { font-size: 0.5em; }
sup { font-size: 0.8em; }
```

[Demo](https://codepen.io/Mathdrquinn/pen/LvJpdY)

### Enhancing Fonts Dynamically

The pinch and zoom functionality granted by the viewport meta tag is awesome, but insufficient.

* Pinching to a comfrotable font-size and scrolling to and fro is a hinderance to users with severe eye-sight impairment
* Larger than normal font can be beneficial to those riding in vehicles where their environment is unsteady.

A quick solution:

```html
<style id="font-base">
  html { font-size: 100%; }
  h1 { font-size: 4rem; }
  p { font-size: 1.25em; }
</style>
<script type="text/javascript">
  function bumpIt(percent) {
    // gets html element
    document.documentElement
      // yay specificity
      .setAttribute('style', `font-size: ${percent}%`)
  }
</script>
<main>
  <form action="#" role="form">
    <fieldset name="font">
      <legend>Set Text Size</legend>
      <label>
        <input type="radio" name="size" value="100" checked onchange="bumpIt(this.value)">
        Standard
      </label>
      <label>
        <input type="radio" name="size" value="150" onchange="bumpIt(this.value)">
        Large
      </label>
      <label>
        <input type="radio" name="size" value="250" onchange="bumpIt(this.value)">
        Extra Large
      </label>
    </fieldset>
  </form>
  <h1>The title of this post</h1>
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum</p>
</main>
```
[Demo](https://codepen.io/Mathdrquinn/pen/LvJpdY)

* Prioritize the form to aid user's beofre they bounce
* Easy to implement in a no JS solution (though this will save data if JS is present)

## Summary

* Good HTML is the foundation of an accessible web site
* Many more users can be delighted by their experience of the web with some slight CSS enhancements
* Be curious, ask why you leave sites, learn the functionality behind the ones you love
* Try a week without a mouse, switch browsers, try working a day with a blind-fold -- its silly-fun and you'll learn something about other people