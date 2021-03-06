<h1 align="center">docs-searchbar.js</h1>

<h4 align="center">
  <a href="https://github.com/meilisearch/MeiliSearch">MeiliSearch</a> |
  <a href="https://www.meilisearch.com">Website</a> |
  <a href="https://blog.meilisearch.com">Blog</a> |
  <a href="https://twitter.com/meilisearch">Twitter</a> |
  <a href="https://docs.meilisearch.com">Documentation</a> |
  <a href="https://docs.meilisearch.com/faq">FAQ</a>
</h4>

<p align="center">
  <a href="https://www.npmjs.com/package/docs-searchbar.js"><img src="https://img.shields.io/npm/v/docs-searchbar.js.svg" alt="NPM version"></a>
  <a href="https://github.com/meilisearch/docs-searchbar.js/actions"><img src="https://github.com/meilisearch/docs-searchbar.js/workflows/Tests-Lint/badge.svg" alt="Test"></a>
  <a href="https://github.com/meilisearch/meilisearch-php/blob/master/LICENSE"><img src="https://img.shields.io/badge/license-MIT-informational" alt="License"></a>
  <a href="https://slack.meilisearch.com"><img src="https://img.shields.io/badge/slack-MeiliSearch-blue.svg?logo=slack" alt="Slack"></a>
</p>

**docs-searchbar.js** is a front-end SDK for **MeiliSearch** to handle your search bar documentation.

**MeiliSearch** is a powerful, fast, open-source, easy to use and deploy search engine. Both searching and indexing are highly customizable. Features such as typo-tolerance, filters, and synonyms are provided out-of-the-box. Features such as typo-tolerance, filters, and synonyms are provided out-of-the-box.

![docs-searchbar.js example](/assets/docs-searchbar-example.png)

If you use Vuepress for your documentation, you might check out our [Vuepress plugin for MeiliSearch](https://github.com/meilisearch/vuepress-plugin-meilisearch).

## Table of Contents <!-- omit in toc -->

- [Installation and Usage](#installation-and-usage)
  - [Before using the library](#before-using-the-library)
  - [Basic Usage](#basic-usage)
  - [Customization](#customization)
- [Development Workflow](#development-workflow)
- [Credits](#credits)

## Installation and Usage

### Before using the library

The goal of this library is to provide very easy integration of a search bar into your own documentation. To make that possible, you need to gather your website content in advance, and index it in a MeiliSearch instance.

Luckily, we provide all the tools that you need, and can help you through the whole process, if you follow [this guide](https://docs.meilisearch.com/resources/howtos/search_bar_for_docs.html) 🚀

As a first introduction, you might only want to test this library without connecting it to your website.<br>
You can do it by running the playground provided in this repository:

```bash
$ yarn install
$ yarn serve
```

The data comes from MeiliSearch documentation.<br>
Type `create an indxe` to live the MeiliSearch experience with the [typo tolerance](https://docs.meilisearch.com/guides/advanced_guides/typotolerance.html).

### Basic Usage

```html
<!DOCTYPE html>
<html>
  <head>
    <link
      rel="stylesheet"
      href="https://cdn.jsdelivr.net/npm/docs-searchbar.js@{version}/dist/cdn/docs-searchbar.min.css"
    />
  </head>

  <body>
    <input type="search" id="search-bar-input" />
    <script src="https://cdn.jsdelivr.net/npm/docs-searchbar.js@{version}/dist/cdn/docs-searchbar.min.js"></script>
    <script>
      docsSearchBar({
        hostUrl: "https://mymeilisearch.com",
        apiKey: "XXX",
        indexUid: "docs",
        inputSelector: "#search-bar-input",
        debug: true // Set debug to true if you want to inspect the dropdown
      });
    </script>
  </body>
</html>
```

The `hostUrl` and the `apiKey` fields are the credentials of your MeiliSearch instance.<br>
`indexUid` is the index identifier in your MeiliSearch instance in which your website content is stored.<br>
`inputSelector` is the `id` attribute of the HTML search input tag.

_Your documentation content is not indexed yet? Check out [this tutorial](https://docs.meilisearch.com/resources/howtos/search_bar_for_docs.html)._

**WARNING: We recommend providing the MeiliSearch public key, which is enough to perform search requests.<br>
Read more about [MeiliSearch authentication](https://docs.meilisearch.com/guides/advanced_guides/authentication.html).**

### Customization

The default behavior of this library fits perfectly for a documentation search bar, but you might need some customizations.

- [Optional parameters](#optional-parameters-) (when calling `docsSearchBar` method)
- [Styling](#styling-) (with CSS)

#### Optional parameters <!-- omit in toc -->

When calling the `docsSearchBar` method, you can add optional fields:

##### `queryHook` <!-- omit in toc -->

`queryHook` takes a callback function as value. This function will be called on every keystroke to transform the typed keywords before querying MeiliSearch. By default, it does not do anything, but it is the perfect place for you to add some preprocessing or custom functionality.

##### `transformData` <!-- omit in toc -->

`transformData` takes a callback function as value. This function will be called on every hit before displaying them. By default, it does not do anything, but it lets you add any post-processing around the data you received from MeiliSearch.

##### `handleSelected` <!-- omit in toc -->

`handleSelected` takes a callback function a value. This function is called when a suggestion is selected (either from a click or a keystroke). By default, it displays anchor links to the results page. Here is an example to override this behavior:

```javascript
docsSearchBar({
  // ...
  handleSelected: function(input, event, suggestion, datasetNumber, context) {
    // Prevents the default behavior on click and rather opens the suggestion
    // in a new tab.
    if (context.selectionMethod === "click") {
      input.setVal("");

      const windowReference = window.open(suggestion.url, "_blank");
      windowReference.focus();
    }
  }
});
```

Note that, by default, you can already open a new tab thanks to the CMD/CTRL + Click action.

The function is called with the following arguments:

- `input`: a reference to the search input element. It comes with the `.open()`, `.close()`, `.getVal()` and `.setVal()` methods.

- `event`: the actual event triggering the selection.

- `suggestion`: the object representing the current selection. It contains a `.url` key representing the destination.

- `datasetNumber`: this should always be equal to 1 as `docs-searchbar.js` is searching into one dataset at a time. You can ignore this attribute.

- `context`: additional information about the selection. Contains a `.selectionMethod` key that can be either `click`, `enterKey`, `tabKey` or `blur`, depending on how the suggestion was selected.

##### `meilisearchOptions` <!-- omit in toc -->

You can forward search parameters to the MeiliSearch API by using the `meilisearchOptions` key. Checkout out the [MeiliSearch documentation about search parameters](https://docs.meilisearch.com/guides/advanced_guides/search_parameters.html#search-parameters).

For example, you might want to increase the number of results displayed in the dropdown:

```javascript
docsSearchBar({
  meilsearchOptions: {
    limit: 10
  }
});
```

##### More Examples <!-- omit in toc -->

Here is a basic [HTML file](scripts/playground.html) used in the playground of this repository.

As a more concrete example, you can [check out the configuration](https://github.com/meilisearch/vuepress-plugin-meilisearch/blob/bf2210e131eb5630f632d514f35710ee6576ec7a/MeiliSearchBox.vue#L64) applied in the MeiliSearch plugin for Vuepress.

#### Styling <!-- omit in toc -->

```css
/* Main dropdown wrapper */
.meilisearch-autocomplete .dsb-dropdown-menu {
  width: 500px;
}

/* Main category */
.meilisearch-autocomplete .docs-searchbar-suggestion--category-header {
  color: darkgray;
  border: 1px solid gray;
}

/* Category */
.meilisearch-autocomplete .docs-searchbar-suggestion--subcategory-column {
  color: gray;
}

/* Title */
.meilisearch-autocomplete .docs-searchbar-suggestion--title {
  font-weight: bold;
  color: black;
}

/* Description */
.meilisearch-autocomplete .docs-searchbar-suggestion--text {
  font-size: 0.8rem;
  color: gray;
}

/* Highlighted text */
.meilisearch-autocomplete .docs-searchbar-suggestion--highlight {
  color: blue;
}
```

**TIPS: When inspecting the dropdown markup with your browser tools, you should add `debug: true` to your `docsSearchBar` call to prevent it from closing on inspection.**

##### More Examples <!-- omit in toc -->

Here is the [CSS customization](https://github.com/meilisearch/vuepress-plugin-meilisearch/blob/bf2210e131eb5630f632d514f35710ee6576ec7a/MeiliSearchBox.vue#L77-L177) applied in the MeiliSearch plugin for Vuepress.

## Development Workflow

If you want to contribute, this section describes the steps to follow.

Thank you for your interest in a MeiliSearch tool! ♥️

### Requirements <!-- omit in toc -->

To run this project, you will need:

- Node.js >= v8.7.0, use nvm - [installation instructions](https://github.com/creationix/nvm#install-script)
- Yarn

### Install <!-- omit in toc -->

Install all the dependencies:

```bash
$ yarn install
```

### Build <!-- omit in toc -->

```bash
$ yarn build
```

This command will build all files in `./dist`. This includes regular and minified files for `<script>` inclusion, as well as classes `import`.

The command itself is split into `yarn run build:js` and `yarn run build:css` if you want to build a subset.

### Serve <!-- omit in toc -->

A playground is provided to test the library.

```bash
$ yarn serve
```

Now, you can have all the files served on `localhost`, along with live-reload.

### Tests and Linter <!-- omit in toc -->

Each PR should pass the tests and the linter to be accepted.

```bash
# Run all the tests:
$ yarn test
# With auto-reload:
$ yarn test:watch
# Linter
$ yarn lint
# Linter with auto-correct
$ yarn lint-fix
# Prettier for markdown (auto-correct)
$ yarn format:md
# Prettier for scss (auto-correct)
$ yarn format:scss
```

### Release <!-- omit in toc -->

MeiliSearch tools follow the [Semantic Versioning Convention](https://semver.org/).

You must do a PR modifying the files `package.json` and `src/lib/version.js` with the right version.

```javascript
"version": "X.X.X",
```

```javascript
export default "X.X.X";
```

Once the changes are merged on `master`, in your terminal, you must be on the `master` branch and push a new tag with the right version:

```bash
$ git checkout master
$ git pull origin master
$ git tag vX.X.X
$ git push --tag origin master
```

A GitHub Actions will be triggered and push the package on [npm](https://www.npmjs.com/package/docs-searchbar.js).

## Credits

Based on [Algolia DocSearch repository](https://github.com/algolia/docsearch) from [this commit](https://github.com/algolia/docsearch/commit/4c32b6f80b753f592de83351116664bf74b10297).<br>
Due to a lot of future changes in this repository compared to the original one, we don't maintain it as an official fork.

<hr>

**MeiliSearch** provides and maintains many **SDKs and Integration tools** like this one. We want to provide everyone with an **amazing search experience for any kind of project**. If you want to contribute, make suggestions, or just know what's going on right now, visit us in the [integration-guides](https://github.com/meilisearch/integration-guides) repository.
