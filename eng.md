<article class="post-151941 post type-post status-publish format-standard hentry
category-coding tag-css tag-semantics post
">
Before 1998, the birth year of CSS Level 2, form elements were already widely
implemented in all major browsers. The CSS 2 specification did not address the 
problem of**how form elements should be presented to users**. Because these
elements are part of the UI of every Web document, the specification’s authors 
preferred to leave the visual layout of such elements to the default style sheet
of Web browsers.

Through the years, this lack of detail in the CSS specification has forced Web
developers to produce a significant number of tests and examples whose primary 
goal is to reduce form elements to a common visual denominator in order to get a
cross-browser rendering of elements such as`input`, `select`, `fieldset`, 
`legend` and `textarea`. In this article, we will cover some of the CSS
patterns used by Web developers to tame the visual layout of form elements.

### Roger Johansson’s Tests

Back in 2004 and later in 2007, Roger Johansson created a complete test suite
for form elements and CSS. These seminal tests, which can be found in his 
article
“[Styling Form Controls With CSS, Revisited][1][1][2],” lead to the
frustrating conclusion that Johansson summarizes with the following words:

> “So what does this experiment show? Like I already stated, it shows that
> using CSS to style form controls to look exactly the same across browsers and 
> platforms is impossible. It also shows that most browsers ignore many CSS 
> properties when they are applied to form controls.
> ”

Despite the underlying truth of these conclusions, Web developers continued to
extensively test CSS styles on form elements to find the Holy Grail of — or at 
least a reasonable compromise between — the browser’s default rendering and the 
author’s styles.

### The Default Model

The CSS 2.1 specification states in its proposed 
[default style sheet for HTML4][3][2][4] that form elements such as `textarea`
`input` and `select` are inline-block elements:

    textarea, input, select {
       display: inline-block;
    }

Conversely, the `form` and `fieldset` elements are block-level elements:

    fieldset, form {
       display: block;
    }

The default model proposed by the CSS specification stops here. All other
visual aspects of form elements**rely on the browser’s default style sheet**.
However, the above rules indicate the following:

*   Inline-block elements can be styled using an inline formatting context.
    This implies the use of CSS properties such as
   `line-height` and `vertical-align` to control the height of the box and its
    vertical alignment. Padding and margins can also be applied to define the outer 
    and inner spacing of the affected box. As well, inline-block elements accept 
    widths and heights because they share the block formatting model.
   
*   Block-level elements can be styled using the well-known block formatting
    context. However, problems arise with the
   `fieldset` and `legend` elements because `legend` relies entirely on the
    default styles provided by Web browsers.
   

How do Web developers manage these problems?

### Defining Dimensions

Web developers soon noticed that Web browsers handle inline-block elements
oddly when it comes to defining dimensions. Defining an height often leads to 
unexpected results:

    input, select {
       width: 120px;
       height: 32px;
    }

Developers tried to fix this problem by turning these elements into block-level
elements:

    input, select {
       width: 120px;
       height: 32px;
       display: block;
    }

Results are still poor, except for the `textarea` element. A common pattern to
solve this problem is to avoid the`height` property and instead to use the 
`font-size` and `padding` properties.

Browsers do not use the same font family and size on these elements, so the
first thing to do is normalize them:

    input, select {
       width: 120px;
       font: 1em Arial, sans-serif;
    }

For an example, see the page “[CSS: Defining Form Element Dimensions][5][3][6]

Once the font in use is normalized, you can add padding to give some inner
spacing to the element’s box:

    input, select {
       width: 120px;
       font: 1em Arial, sans-serif;
       padding: 3px 6px;
    }

The `input` elements and `textarea` also show a border that affects their box
model:

    input[type="text"],
    input[type="password"],
    textarea {
       border: 1px solid #ccc;
    }

The `input` elements of the types `button` and `submit` have additional padding
set by Web browsers. So, a common practice is to normalize them:

    input[type="button"],
    input[type="submit"] {
       padding: 2px;
    }

The problem with this approach is that **Web browsers also apply vendor-
specific properties** to these elements, so our padding is not always able to
normalize this property. For example, in a Webkit-based browser, you might have 
this:

    input[type="button"], input[type="submit"],
    input[type="reset"], input[type="file"]::-webkit-file-upload-button,
    button {
       -webkit-box-align: center;
       text-align: center;
       cursor: default;
       color: buttontext;
       padding: 2px 6px 3px;
       border: 2px outset buttonface;
       border-image: initial;
       background-color: buttonface;
       box-sizing: border-box;
    }
    input[type="button"], input[type="submit"], input[type="reset"] {
       -webkit-appearance: push-button;
       white-space: pre;
    }

Padding is also used on the `fieldset` and `legend` elements but with different
results:

*   Setting the padding on `fieldset` to `` will reset the default indentation
    of the
   `legend` elements in some browsers (though not in IE).
*   Setting the padding on `legend` to `` has the effect of making this element
    shrink.
   

Select boxes, checkboxes and radio buttons can be normalized with good results
with only a few properties, namely:

*   `font-family`,
*   `font-size`,
*   `width` (on select boxes),
*   `padding`.

Applying other properties to this group of elements often leads to inconsistent
results across browsers.

### Alignment

Form elements can be vertically or horizontally aligned. They can be laid out
on the same line or as a group of boxes on multiple rows. To align them on the 
same line, you can follow one of two approaches:

1.  Use floating,
2.  Use the default inline-block context on some of these elements.

When you use floating, elements are automatically turned into block-level
elements. This means that form elements are now subject to the
[nine rules that govern floated elements][7][4][8].

![vertical-grid1][9][5][10]  
*Form elements can be vertically or horizontally aligned.*

With floats, the main challenge is to achieve good vertical alignment on the
current line. In this case, using vertical margins or padding is a common 
practice:

    input, select {
       width: 120px;
       float: left;
       margin-top: 0.4em;
    }

This approach works when you do not have to align boxes with text, such as with
the contents of a`label` element. In this case, you could use **relative
positioning, padding or margins** on the element that contains only text:

    label {
       float: left;
       padding-top: 0.4em;
       width: 5em;
       margin-right: 1em;
    }

Another problem arises with buttons. In this case, when you have a button whose
dimensions are greater than those of other elements, you can force its vertical 
alignment with relative positioning:

    input[type="submit"] {
       float: left;
       width: 90px;
       position: relative;
       top: 0.4em;
    }

This approach with **relative positioning also works with checkboxes and radio
buttons**. Relative positioning can even be applied to normalize the left
indentation of the`legend` element within a `fieldset` element, the only
difference being that you would use the`left` property instead of `top`.

When using inline formatting, you can rely on the `vertical-align` property to
vertically align elements:

    label, input[type="text"] {
       vertical-align: middle;
       margin-right: 1em;
    }

Good results can be achieved by combining this property with the `line-height`
property. However, this property must be set on the parent element. If you set 
this property directly on the form’s elements, their computed height would be 
affected:

    .form-row {
       line-height: 1.4;
    }

Using a declared height on the parent element is also effective when paired
with the same value for the line height:

    .form-row {
       line-height: 1.8;
       height: 1.8em;
    }

With inline formatting, you can also use the `text-align` property on the
parent element to align elements to the right, left or center.

### The Strange Case Of File Inputs

The file input element — that is, `<input type="file"/>` — is a special
case. This kind of element must always be visible and recognizable in the 
browser’s UI for security reasons. This implies that browsers deliberately 
ignore some style rules (such as those related to visibility) and tend to apply 
the algorithms defined in their default style sheet.

Furthermore, the default rendering varies from browser to browser: some
browsers display only a single button, while others add a text field to display 
the path of the uploaded file.

Web developers, however, soon found a way to circumvent these limitations.
First, they wrapped the input element in a container:

    <form action="" method="post" enctype="multipart/form-data">
       <div class="upload">
          <input type="file" name="upload"/>
       </div>
    </form>

Then, they hid the input element using the `opacity` property and applied
certain styles to the input’s container:

    .upload {
       width: 157px;
       height: 57px;
       background: url(upload.png) no-repeat;
       overflow: hidden;
    }
    
    .upload input {
       display: block !important;
       width: 157px !important;
       height: 57px !important;
       opacity: 0 !important;
       overflow: hidden !important;
    }​

Notice the `!important` statement. This is the preferred way to override the
browser’s default rules.

For an example, see the page “[CSS: Styling Inputs of Type ‘file’][11][6][12]

### Conclusion

Totally taming form elements is impossible due to the lack of detail in the CSS
specification and because of the default styles applied by Web browsers. However,
by following some common practices, reducing (though not eliminating) the 
differences and achieving good visual results are possible.

*(al)*

#### Footnotes

[↑ Back to top][13][Share on Twitter][14]</article>


 [1]: http://www.456bereastreet.com/archive/200701/styling_form_controls_with_css_revisited/
 [2]: http://www.smashingmagazine.com/2013/02/27/css-form-elements-problem/#1
 [3]: http://www.w3.org/TR/CSS21/sample.html
 [4]: http://www.smashingmagazine.com/2013/02/27/css-form-elements-problem/#2
 [5]: http://jsfiddle.net/gabrieleromanato/J2nUn/
 [6]: http://www.smashingmagazine.com/2013/02/27/css-form-elements-problem/#3
 [7]: http://www.w3.org/TR/CSS21/visuren.html#floats
 [8]: http://www.smashingmagazine.com/2013/02/27/css-form-elements-problem/#4
 [9]: img/vertical-grid1.jpg
 [10]: http://www.smashingmagazine.com/2013/02/27/css-form-elements-problem/#5
 [11]: http://jsfiddle.net/gabrieleromanato/mxq9R/
 [12]: http://www.smashingmagazine.com/2013/02/27/css-form-elements-problem/#6

 [13]: http://www.smashingmagazine.com/2013/02/27/css-form-elements-problem/#top "Jump to the top of the page"

 [14]: https://twitter.com/intent/tweet?original_referer=http%3A%2F%2Fwww.smashingmagazine.com%2F2013%2F02%2F27%2Fcss-form-elements-problem%2F&source=tweetbutton&text=The%20Problem%20Of%20CSS%20Form%20Elements&url=http%3A%2F%2Fwww.smashingmagazine.com%2F2013%2F02%2F27%2Fcss-form-elements-problem%2F&via=smashingmag "Share on Twitter!"