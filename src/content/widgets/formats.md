### Default Formats
When you use a LiveWhale widget, you are often using the default format when each item is output in a list. For example, the default format for a news widget is:

    {image}
    <div class="lw_news_headline">
      {headline}
    </div>
    <div class="lw_news_summary">
      {summary}
    </div>

In the example above, each curly-braced {format_variable} will be replaced with the value from each item in the matching widget stories. The values may be simply text or HTML. For instance, if at least one image is attached to the story, the above {image} would be replaced by an `<img/>` set to the size of the thumbnail, wrapped in a link `<a>` to the full story, wrapped in a `<span>` when output in a page or inside other dynamic content.

When the item value being requested is empty or not set, then the {format_variable} disappears entirely from the output of that item in the widget. In such cases, you would not see {image} above at all, but when markup is surrounding the variable, as for {summary} an empty summary results in `<div class="lw_news_summary"></div>` still present in the output. (See conditional formatting below if this is an issue for your widget.)

### Creating/Editing Formats
In many widgets, you have the opportunity when editing a widget to set your own widget format, and additionally one for starred items when the content can be starred.

You can modify the widget output by clicking any of the available buttons directly beneath the widget format to add the format variable and any default wrapper markup. In many cases, this default markup— particularly the classes— will tie your widget into the default LiveWhale design, or into the custom design that your site uses.

You can also simply click into the format and add/change/delete anything in there, so long as the markup validates as HTML. Want that `<span>` to be a `<div>` instead? Just change it. Do note that such changes should accompany a review of design (CSS) and function (javascript) for your site.

If you’re working on a custom project, where you’ll be also be updating or adding CSS/javascript, then you can use any markup in the format. Use your own ids, classes, etc. as needed to achieve your end. (And read about the special format variables below, as you may need more options than those provided via the buttons.)

#### Other Variables
When doing custom formats, you are also not limited to the values for the buttons available. If you wish, you can include the values for fields based on their name in the matching editor. For example, you can include {body} in a news widget to get the entire body of the story. Or, try {description} for an event.

### Special Format Variables
When creating a very specific form of output, you may need the item values output in a raw state, so that you can fully customize the markup. For example, with the above {image} you would have no way to not wrap the image in a link or span.

While not available as buttons, you can manually type in any number of special variables that are populated whenever the base item value is non-empty. The attached image has probably the largest number of options:

{image}: `<span class="lw_item_thumb"><a href="{href}"><img src="{image_src}" alt="Image preview" class="lw_image" height="{thumb_height}" width="{thumb_width}"/></a></span>`

{image_src}: an absolute path to the image, scaled to thumbnail size
{image_href}: alias to {image_src}
{image_caption}: the image caption

{link}: `<a href="{href}">{link_text}</a>`
{href}: the absolute URL to the full content of the item, e.g. its details page

#### _clean

When {format_variables} come pre-formatted as markup or otherwise might include HTML (summary, body, description, etc.) you can append _clean to any variable name to get its value as raw text. For example:

__{headline}:__ `<a href="{href}">{headline_text}</a>`
__{headline_clean}:__ the headline as text only

__{title}:__ `<a href="{href}">{title_clean}</a>`
__{title_clean}:__ the title as text only

__{tags}:__ `<p><strong>Tags:</strong><span class="lw_item_tags"><a href="...">tag</a>, ...</span></p>`
__{tags_clean}:__ tag, tag2, tag3,...

### Parent Format Variables
When a widget appears inside another widget, the nested widget will also have access to the item variables of the containing widget, so long as the nested one doesn’t use the same variable name. This obviously gets very Inception-like, but you could nest a blurb widget inside a news story, and know that you can put the {headline_clean} in the blurb widget format.

There is one caveat to this, while {headline} is always available for widget output, other less-used values like {group_title} are only available to the nested widget when they are also in the containing widget. If you have an instance where a value isn’t needed in the containing widget, you will still need to include it, but in a hidden form, as: `<div style="display:none;">{format_variable_just_for_nested_use}</div>`.

### Simple Conditional Formatting
Beginning in LiveWhale 1.4.3, conditional format variables became available in order to fine-tune the markup output when a value was empty. Using the news summary example above, you could make the `<div>` surrounding the {summary} conditional on the summary being available by changing the widget format to:

    {image}
    <div class="lw_news_headline">
      {headline}
    </div>
    {<div class="lw_news_summary">
      |summary|
    </div>}

In the above format, the `<div>` wrapping summary is now inside the curly braces, with the actual variable name offset with vertical pipes (|). With this simple templating style , you could have any HTML markup conditional on the non-emptiness of the item value.

### Nested Conditional Formatting
Beginning in LiveWhale 1.4.5 and 1.5.0, conditional variables gained the ability to be nested inside each other. For example, perhaps you want the ability to show one item value only if another is also non-empty. Let’s presume that we only want to show a news story headline and summary conditional on the non-emptiness of the summary. That would look like this:

    {image}
    {<div class="lw_news_headline">
      {headline}
    </div>
    <div class="lw_news_summary">
      |summary|
    </div>}

In the above example, the summary curly braces now surround everything but the {image} and like the simple conditional, the summary is bound by vertical pipes. But within the summary braces, we now see the headline, conditional upon the summary. You can include as many other {format_variables} as you please within a conditional set of braces, all dependent on the primary value.

And if you wish, you can go even deeper:

    {image}
    {<div class="lw_news_headline">
      {<span class="lw_news_group_title">
        |group_title|:
      </span>}
      {headline}
    </div>
    <div class="lw_news_summary">
      |summary|
    </div>}

In this final example, the `<span>` surrounding the group title is conditional on the group title, while it, the headline and the summary are all conditional on the summary. At present, nested conditionals are allowed to at least two levels, as shown above. (Do you really need more?)