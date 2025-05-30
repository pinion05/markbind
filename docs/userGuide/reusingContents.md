{% set title = "Reusing Contents" %}
{% set filename = "reusingContents" %}
<span id="title" class="d-none">{{ title }}</span>

<frontmatter>
  title: "User Guide: {{ title }}"
  layout: userGuide.md
  pageNav: 4
</frontmatter>

<span id="link" class="d-none">
<md>[_User Guide → {{ title }}_]({{ filename }}.html)</md>
</span>

# {{ title }}

<div class="lead" id="overview">

**MarkBind is highly-optimized for content reuse**. It offers several mechanisms to provide readers with many variations of the content while minimizing duplication at source file level. As a result, instead of creating a one-size-fits-all site, MarkBind can create a site in which readers can chart their own path of reading.
</div>


<include src="syntax/variables.md" />

<hr><!-- ======================================================================================================= -->

<include src="syntax/includes.md" />

<hr><!-- ======================================================================================================= -->

## Reusing Contents Across Sites

**MarkBind supports reusing across sites.** It allows you to include the pages you want from a _sub-site_ in another _main-site_ without having to change anything in the source files of the _sub-site_ as long as the _sub-site_ source files are inside the directory of the _main-site_.

<div class="indented">

{{ icon_example }} Suppose you have a site `textbook` and you want to include some pages from it in another site `course`. Given below is how you can locate the sub-site `textbook` inside the root directory of the main-site `course` so that files from `textbook` can be reused in the `course` site.
<tree>
C:/course/
  textbook/
    index.md
    overview.md
    site.json
  index.md
  reading.md
  site.json
</tree>

In `reading.md` (note how it reuses content from the sub-site `textbook`):
```markdown
# Week 1 Reading:
<include src="textbook/overview.md" />
```
</div>

<include src="tip.md" boilerplate >
<span id="tip_body">
If you are using Git for version control, you can set up the sub-site repository as a [Git sub-module](https://git-scm.com/book/en/v2/Git-Tools-Submodules) of the main site repository.
</span>
</include>

<hr><!-- ======================================================================================================= -->

## Creating Content Variations

**MarkBind can create sites that give more control to the reader.** Given below are some mechanisms authors can use to create variations of content that gives more control to the reader in charting their own path through the content.


#### Allowing users to remove some contents

When the readers can remove an item from a page, they can create their own version of the page by removing items they don't want to see. This is especially useful when printing a page.

To make an element closeable, use `v-closeable`.

<div class="indented">

```html
<div v-closeable>

Optional video:

@[youtube](v40b3ExbM0c)

</div>
```

This is how the content will appear. Note how you can hover over the content to access the :x: button that can collapse the content.
<div v-closeable>

Optional video:

@[youtube](v40b3ExbM0c)

</div>

</div>

#### Giving alternative contents

You can use a [_Tabs_ component](components/presentation.html#tabs) to give alternative versions of content, for example, giving a code snippet in different programming languages.

#### Giving access to additional contents

You can use following components to give readers an option to access additional content at their discretion.
* [Tooltips](components/popups.html#tooltips), [Popovers](components/popups.html#popovers), [Modals](components/popups.html#modals)
* [Expandable Panels](components/presentation.html#panels)

#### Organizing contents in alternative ways

You can take advantage of [MarkBind's feature for content reuse](reusingContents.html) to organize content in alternative ways to cater for different readers, without having to duplicate content. For example, you can have different pages that organizes the same information alphabetically, chronologically, by difficulty, group information by topic, etc.

#### Optimizing the Print View

MarkBind comes with some built-in optimizations for printing by default:
- Automatically hiding minimized panels in the _print view_
- <include src="syntax/code.md#code-print-optimization" />

#### Hiding some info in the generated content

To permanently hide a fragment from the reader:

```html
<span class="d-none">
  content to hide ...
</span>

<panel header="..." add-class="d-none">
  content to hide ...
<panel>
```

To hide a fragment in one specific page, 'mark' the elements using a `class`:
```html
<span class="extra">
  content to hide ...
</span>
```

Then, in a page-specific CSS file,
```css
.extra {
  display: none; /* 'block' or 'inline-block' if you want it to show */
}
```

#### Deploying a page multiple times with different titles

By [overriding the `title` declared in the frontmatter of the page using `site.json`](tweakingThePageStructure.html#frontmatter), it is possible to allow MarkBind to serve the same page with different titles. 

This may especially be useful for users who are serving a page from a submodule.

#### Creating slight variations of content

Tags are a good way to create multiple variations of a page within the same source file, such as to filter content for creating multiple different versions of the same page. See [_User Guide: Tweaking the Page Structure → Tags_](tweakingThePageStructure.html#plugin-tags) section for more information.

{% from "njk/common.njk" import previous_next %}
{{ previous_next('tweakingThePageStructure', 'workingWithSites') }}
