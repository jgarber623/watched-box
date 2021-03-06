# Watched Box

**Declarative container queries FTW!**

A custom element as a `ResizeObserver` root. The easiest way to get working with `ResizeObserver` and creating [container queries](https://ethanmarcotte.com/wrote/on-container-queries/) with JavaScript. Unlike many implementations, you can use _any_ CSS length units, and mix them together.

## Get started

Just import the `watched-box.min.js` module, like so:

```js
import WatchedBox from './path/to/watched-box.min.js';
```

Then you can start wrapping your content in `<watched-box>` Custom Elements and defining `width` and `height` breakpoints:

```html
<watched-box widthBreaks="70ch, 900px" heightBreaks="50vh, 60em">
  <!-- HTML and text stuff here -->
</watched-box>
```

This will generate your classes, which take the following format (where a prefix is not supplied; see [Props](#props)):

* Less than or equal to the supplied **width**: `w-lte-[the width]`
* Greater than the supplied **width**: `w-gt-[the width]`
* Less than or equal to the supplied **height**: `h-lte-[the height]`
* Greater than the supplied **height**: `h-gt-[the height]`

Depending on the aspect ratio, one of the following classes will also be appended (where a prefix is not supplied; see [Props](#props)):

* `portrait`
* `landscape`
* `square`

Finally, it is recommended you set `watched-box` to `display: block` by default in your CSS. You'll rarely be working with `<watched-box>` elements.

```css
watched-box {
  display: block;
}
```

## Examples

### Single breaks for each dimension

A box with a width of `405px` and a height of `9.5em`.

![A box that is 405px wide and 9.5em high](illustrations/405px_by_9.5em.png)

```html
<watched-box widthbreaks="405px" heightbreaks="9em" class="w-lte-405px h-gt-9em landscape"></watched-box>

### Multiple breaks for each dimension, using different units

A box with a width of `24ch` and a height of `120px`.

![A box that is 24ch wide and 120px high](illustrations/24ch_by_120px.png)

```html
<watched-box widthbreaks="9em, 800px" heightbreaks="405px, 10vh" class="w-gt-9em w-lte-800px h-lte-405px h-gt-10vh landscape"></watched-box>
```

Note that units are dynamically converted for comparison, meaning you can mix them. Where the context's value for `1em` is `21px`, `<watched-box>` is aware that `3.1em` is longer than `63px` (`21px` * 3, or `3em`).

### Exploring relativity

![A box that is 200px squared](illustrations/200px_by_200px.png)

This one is more complex, and shows the power of `<watched-box>`. The element has the following CSS:

```css
watched-box {
  font-size: 20em;
  width: 200px;
  height: 200px;
}
```

Note the large `20em` `font-size`. This is likely (depending on the context) to make `1em` _greater_ than `201px`, eliciting the `w-lte-1em` class as below.

```html
<watched-box widthbreaks="201px, 1em" heightbreaks="199px" class="w-lte-201px w-lte-1em h-gt-199px square"></watched-box>
```

Here's the same result, but with the `prefix="wbx"` prop/value supplied:

```html
<watched-box widthbreaks="201px, 1em" heightbreaks="199px" prefix="wbx" class="wbx-w-lte-201px wbx-w-lte-1em wbx-h-gt-199px wbx-square"></watched-box>
```

## Props

Custom elements like `<watched-box>` take props as attributes. When the values change, `<watched-box>` reinitializes `ResizeObserver` observation.

<table>
  <tr>
    <th>Name</th>
    <th>Default</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><code>widthBreaks</code></td>
    <td><code>'1024px'</code></td>
    <td>{string} A comma-separated list of CSS length values (e.g. <code>widthBreaks="20vw, 65ch, 300px, 900px"</code>)</td>
  </tr>
  <tr>
    <td><code>heightBreaks</code></td>
    <td><code>'768px'</code></td>
    <td>{string} A comma-separated list of CSS length values (e.g. <code>heightBreaks="50vh, 800px, 9.25em, 200em"</code>)</td>
  <tr>
    <td><code>prefix</code></td>
    <td><code>null</code></td>
    <td>{string} A prefix for each generated class, for namespacing purposes (e.g. <code>prefix="wbox"</code>)</td>
  </tr>
</table>

## Workflow

Obviously, `<watched-box>` doesn't write CSS for you (sorry to disappoint!). However, `<watched-box>` is just an element, and can be attributed and targeted with CSS however you like.

In the following example, I have created a specific `<watched-box>` and identified it with the class `.grid`. Imagining it has a `30ch` width breakpoint, I can affect a `.cell` child element like so:

```css
.grid.w-lte-30ch .cell {
  /* styles for less-than-or-equal-to 30ch width */
}

.grid.w-gt-30ch .cell {
  /* styles for over 30ch width */
}
```

Naturally, I can use negation too, if I wish:

```css
.grid:not(.w-lte-30ch) .cell {
  /* styles for less-than-or-equal-to 30ch width */
}
```