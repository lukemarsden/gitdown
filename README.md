[![NPM version](http://img.shields.io/npm/v/gitdown.svg?style=flat-square)](https://www.npmjs.org/package/gitdown)
[![Travis build status](http://img.shields.io/travis/gajus/gitdown/master.svg?style=flat-square)](https://travis-ci.org/gajus/gitdown)
[![Dependency Status](https://img.shields.io/david/gajus/gitdown.svg?style=flat-square)](https://david-dm.org/gajus/gitdown)

Gitdown adds [additional functionality](#features) (generating table of contents, including documents, using variables, etc.) to [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/).

<h2 id="cheat-sheet">Cheat Sheet</h2>


```js
// Generate table of contents
{"gitdown": "contents"}
{"gitdown": "contents", "maxLevel": 4}
{"gitdown": "contents", "rootId": "features"}

// Use a custom defined variable
{"gitdown": "variable", "name": "nameOfTheVariable"}

// Include file
{"gitdown": "include", "file": "./LICENSE.md"}

// Get file size
{"gitdown": "filesize", "file": "./src/gitdown.js"}
{"gitdown": "filesize", "file": "./src/gitdown.js", "gzip": true}

// Generate badges
{"gitdown": "badge", "name": "npm-version"}
{"gitdown": "badge", "name": "bower-version"}
{"gitdown": "badge", "name": "travis"}
{"gitdown": "badge", "name": "david"}
{"gitdown": "badge", "name": "david-dev"}
{"gitdown": "badge", "name": "waffle"}

// Print date
{"gitdown": "date", "format": "YYYY"}
```


<h2 id="contents">Contents</h2>

* [Cheat Sheet](#cheat-sheet)
* [Contents](#contents)
* [Command Line Usage](#command-line-usage)
* [API Usage](#api-usage)
    * [Gulp](#api-usage-gulp)
    * [Logging](#api-usage-logging)
* [Syntax](#syntax)
    * [Ignoring Sections of the Document](#syntax-ignoring-sections-of-the-document)
    * [Register a Custom Helper](#syntax-register-a-custom-helper)
* [Features](#features)
    * [Generate Table of Contents](#features-generate-table-of-contents)
    * [Heading Nesting](#features-heading-nesting)
    * [Find Dead URLs and Fragment Identifiers](#features-find-dead-urls-and-fragment-identifiers)
    * [Reference an Anchor in the Repository](#features-reference-an-anchor-in-the-repository)
    * [Variables](#features-variables)
    * [Include File](#features-include-file)
    * [Get File Size](#features-get-file-size)
    * [Generate Badges](#features-generate-badges)
    * [Print Date](#features-print-date)
    * [Gitinfo](#features-gitinfo)


<h2 id="command-line-usage">Command Line Usage</h2>

```sh
npm install gitdown -g
gitdown ./.README/README.md --output-file ./README.md
```

<h2 id="api-usage">API Usage</h2>

Gitdown is designed to be run using either of the build systems, such as [Gulp](http://gulpjs.com/) or [Grunt](http://gruntjs.com/).

```js
const Gitdown = require('gitdown');

// Read the markdown file written using the Gitdown extended markdown.
// File name is not important.
// Having all of the Gitdown markdown files under ./.README/ path is the recommended convention.
const gitdown = Gitdown.readFile('./.README/README.md');

// If you have the subject in a string, call the constructor itself:
// gitdown = Gitdown.read('literal string');

// Get config.
gitdown.getConfig()

// Set config.
gitdown.setConfig({
    gitinfo: {
        gitPath: __dirname
    }
})

// Output the markdown file.
// All of the file system operations are relative to the root of the repository.
gitdown.writeFile('README.md');
```

<h3 id="api-usage-gulp">Gulp</h3>

Gitdown `writeFile` method returns a promise, that will make Gulp wait until the task is completed. No third-party plugins needed.

```js
const gulp = require('gulp');
const Gitdown = require('gitdown');

gulp.task('gitdown', () => {
    return Gitdown
        .readFile('./.README/README.md')
        .writeFile('README.md');
});

gulp.task('watch', () => {
    gulp.watch(['./.README/*'], ['gitdown']);
});
```

<h3 id="api-usage-logging">Logging</h3>

Gitdown is using `console` object to log messages. You can set your own logger:

```js
gitdown.setLogger({
    info: () => {},
    warn: () => {}
});
```

The logger is used to inform about [Dead URLs and Fragment Identifiers](#find-dead-urls-and-fragment-identifiers).

<h2 id="syntax">Syntax</h2>

Gitdown extends markdown syntax using JSON:


```json
{"gitdown": "helper name", "parameter name": "parameter value"}
```


The JSON object must have a `gitdown` property that identifies the helper you intend to execute. The rest is a regular JSON string, where each property is a named configuration property of the helper that you are referring to.

JSON that does not start with a "gitdown" property will remain untouched.

<h3 id="syntax-ignoring-sections-of-the-document">Ignoring Sections of the Document</h3>

Use HTML comment tags to ignore sections of the document:

```html
Gitdown JSON will be interpolated.
&lt;!-- gitdown: off --&gt;
Gitdown JSON will not be interpolated.
&lt;!-- gitdown: on --&gt;
Gitdown JSON will be interpolated.
```

<h3 id="syntax-register-a-custom-helper">Register a Custom Helper</h3>

```js
gitdown.registerHelper('my-helper-name', {
    /**
     * @var {Number} Weight determines the processing order of the helper function. Default: 10.
     */
    weight: 10,
    /**
     * @param {Object} config JSON configuration.
     * @return {mixed|Promise}
     */
    compile: (config) => {
        return 'foo: ' + config.foo;
    }
});
```


```json
{"gitdown": "my-helper-name", "foo": "bar"}
```


Produces:

```markdown
foo: bar
```


<h2 id="features">Features</h2>

<h3 id="features-generate-table-of-contents">Generate Table of Contents</h3>


```json
{"gitdown": "contents"}
```


Generates table of contents.

The table of contents is generated using [markdown-contents](https://github.com/gajus/markdown-contents).

<h4 id="features-generate-table-of-contents-example">Example</h4>


```json
{"gitdown": "contents", "maxLevel": 4, "rootId": "features"}
```


```markdown
* [Generate Table of Contents](#features-generate-table-of-contents)
    * [Example](#features-generate-table-of-contents-example)
    * [JSON Configuration](#features-generate-table-of-contents-json-configuration)
* [Heading Nesting](#features-heading-nesting)
    * [Parser Configuration](#features-heading-nesting-parser-configuration)
* [Find Dead URLs and Fragment Identifiers](#features-find-dead-urls-and-fragment-identifiers)
    * [Parser Configuration](#features-find-dead-urls-and-fragment-identifiers-parser-configuration)
* [Reference an Anchor in the Repository](#features-reference-an-anchor-in-the-repository)
    * [JSON Configuration](#features-reference-an-anchor-in-the-repository-json-configuration)
    * [Parser Configuration](#features-reference-an-anchor-in-the-repository-parser-configuration)
* [Variables](#features-variables)
    * [Example](#features-variables-example)
    * [JSON Configuration](#features-variables-json-configuration)
    * [Parser Configuration](#features-variables-parser-configuration)
* [Include File](#features-include-file)
    * [Example](#features-include-file-example)
    * [JSON Configuration](#features-include-file-json-configuration)
* [Get File Size](#features-get-file-size)
    * [Example](#features-get-file-size-example)
    * [JSON Configuration](#features-get-file-size-json-configuration)
* [Generate Badges](#features-generate-badges)
    * [Supported Services](#features-generate-badges-supported-services)
    * [Example](#features-generate-badges-example)
    * [JSON Configuration](#features-generate-badges-json-configuration)
* [Print Date](#features-print-date)
    * [Example](#features-print-date-example)
    * [JSON Configuration](#features-print-date-json-configuration)
* [Gitinfo](#features-gitinfo)
    * [Example](#features-gitinfo-example)
    * [Supported Properties](#features-gitinfo-supported-properties)
    * [JSON Configuration](#features-gitinfo-json-configuration)
    * [Parser Configuration](#features-gitinfo-parser-configuration)

```

<h4 id="features-generate-table-of-contents-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `maxLevel` | The maximum heading level after which headings are excluded. | 3 |
| `rootId` | ID of the root heading. Provide it when you need table of contents for a specific section of the document. Throws an error if element with the said ID does not exist in the document. | N/A |
<h3 id="features-heading-nesting">Heading Nesting</h3>

Github markdown processor generates heading ID based on the text of the heading.

The conflicting IDs are solved with a numerical suffix, e.g.

```markdown
# Foo
## Something
# Bar
## Something
```

```html
<h1 id="foo">Foo</h1>
<h2 id="something">Something</h1>
<h1 id="bar">Bar</h1>
<h2 id="something-1">Something</h1>
```

The problem with this approach is that it makes the order of the content important.

Gitdown will nest the headings using parent heading names to ensure uniqueness, e.g.

```markdown
# Foo
## Something
# Bar
## Something
```

```html
<h1 id="foo">Foo</h1>
<h2 id="foo-something">Something</h1>
<h1 id="bar">Bar</h1>
<h2 id="bar-something">Something</h1>
```

<h4 id="features-heading-nesting-parser-configuration">Parser Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `headingNesting.enabled` | Boolean flag indicating whether to nest headings. | `true` |
<h3 id="features-find-dead-urls-and-fragment-identifiers">Find Dead URLs and Fragment Identifiers</h3>

Uses [Deadlink](https://github.com/gajus/deadlink) to iterate through all of the URLs in the resulting document. Throws an error if either of the URLs is resolved with an HTTP status other than 200 or fragment identifier (anchor) is not found.

<h4 id="features-find-dead-urls-and-fragment-identifiers-parser-configuration">Parser Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `deadlink.findDeadURLs` | Find dead URLs. | `false` |
| `deadlink.findDeadFragmentIdentifiers` | Find dead fragment identifiers. | `false` |

<!-- -->
<!--| `deadlink.ignoreURLs` | URLs matching the regex will be ignored. | N/A |-->

<h3 id="features-reference-an-anchor-in-the-repository">Reference an Anchor in the Repository</h3>

> This feature is under development.
> Please suggest ideas https://github.com/gajus/gitdown/issues


```json
{"gitdown": "anchor"}
```


Generates a Github URL to the line in the source code with the anchor documentation tag of the same name.

Place a documentation tag `@gitdownanchor <name>` anywhere in the code base, e.g.

```js
/**
 * @gitdownanchor my-anchor-name
 */
```

Then reference the tag in the Gitdown document:


```
Refer to [foo]({"gitdown": "anchor", "name": "my-anchor-name"}).
```


The anchor name must match `/^[a-z]+[a-z0-9\-_:\.]*$/i`.

Gitdown will throw an error if the anchor is not found.

<h4 id="features-reference-an-anchor-in-the-repository-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `name` | Anchor name. | N/A |

<h4 id="features-reference-an-anchor-in-the-repository-parser-configuration">Parser Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `anchor.exclude` | Array of paths to exclude. | `['./dist/*']` |
<h3 id="features-variables">Variables</h3>


```json
{"gitdown": "variable"}
```


Prints the value of a property defined under a parser `variable.scope` configuration property. Throws an error if property is not set.

<h4 id="features-variables-example">Example</h4>


```js
const gitdown = Gitdown(
    '{"gitdown": "variable", "name": "name.first"}' +
    '{"gitdown": "variable", "name": "name.last"}'
);

gitdown.setConfig({
    variable: {
        scope: {
            name: {
                first: "Gajus",
                last: "Kuizinas"
            }
        }
    }
});
```


<h4 id="features-variables-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `name` | Name of the property defined under a parser `variable.scope` configuration property. | N/A |

<h4 id="features-variables-parser-configuration">Parser Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `variable.scope` | Variable scope object. | `{}` |

<h3 id="features-include-file">Include File</h3>


```json
{"gitdown": "include"}
```


Includes the contents of the file to the document.

The included file can have Gitdown JSON hooks.

<h4 id="features-include-file-example">Example</h4>

See source code of [./.README/README.md](https://github.com/gajus/gitdown/blob/master/.README/README.md).

<h4 id="features-include-file-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `file` | Path to the file. The path is relative to the root of the repository. | N/A |

<h3 id="features-get-file-size">Get File Size</h3>


```json
{"gitdown": "filesize"}
```


Returns file size formatted in human friendly format.

<h4 id="features-get-file-size-example">Example</h4>


```json
{"gitdown": "filesize", "file": "src/gitdown.js"}
{"gitdown": "filesize", "file": "src/gitdown.js", "gzip": true}
```


Generates:

```markdown
9.56 KB
2.62 KB
```

<h4 id="features-get-file-size-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `file` | Path to the file. The path is relative to the root of the repository. | N/A |
| `gzip` | A boolean value indicating whether to gzip the file first. | `false` |
<h3 id="features-generate-badges">Generate Badges</h3>


```json
{"gitdown": "badge"}
```


Gitdown generates markdown for badges using the environment variables, e.g. if it is an NPM badge, Gitdown will lookup the package name from `package.json`.

Badges are generated using http://shields.io/.

<h4 id="features-generate-badges-supported-services">Supported Services</h4>

| Name | Description |
| --- | --- |
| `npm-version` | [NPM](https://www.npmjs.org/) package version. |
| `bower-version` | [Bower](http://bower.io/) package version. |
| `travis` | State of the [Travis](https://travis-ci.org/) build. |
| `david` | [David](https://david-dm.org/) state of the dependencies. |
| `david-dev` | [David](https://david-dm.org/) state of the development dependencies. |
| `waffle` | Issues ready on [Waffle](https://waffle.io/) board. |
| `gitter` | Join [Gitter](https://gitter.im/) chat. |
| `coveralls` | [Coveralls](https://coveralls.io/). |
| `codeclimate-gpa` | [Code Climate](https://codeclimate.com/) GPA. |
| `codeclimate-coverage` | [Code Climate](https://codeclimate.com/) test coverage. |
| `appveyor` | [AppVeyor](http://www.appveyor.com/) status. |

What service are you missing? [Raise an issue](https://github.com/gajus/gitdown/issues).

<h4 id="features-generate-badges-example">Example</h4>


```json
{"gitdown": "badge", "name": "npm-version"}
{"gitdown": "badge", "name": "travis"}
{"gitdown": "badge", "name": "david"}
```


Generates:

```markdown
[![NPM version](http://img.shields.io/npm/v/gitdown.svg?style=flat-square)](https://www.npmjs.org/package/gitdown)
[![Travis build status](http://img.shields.io/travis/gajus/gitdown/master.svg?style=flat-square)](https://travis-ci.org/gajus/gitdown)
[![Dependency Status](https://img.shields.io/david/gajus/gitdown.svg?style=flat-square)](https://david-dm.org/gajus/gitdown)
```

<h4 id="features-generate-badges-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `name` | Name of the service. | N/A |

<h3 id="features-print-date">Print Date</h3>


```json
{"gitdown": "date"}
```


Prints a string formatted according to the given [moment format](http://momentjs.com/docs/#/displaying/format/) string using the current time.

<h4 id="features-print-date-example">Example</h4>


```json
{"gitdown": "date"}
{"gitdown": "date", "format": "YYYY"}
```


Generates:

```markdown
1459240961
2016
```

<h4 id="features-print-date-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `format` | [Moment format](http://momentjs.com/docs/#/displaying/format/). | `X` (UNIX timestamp) |
<h3 id="features-gitinfo">Gitinfo</h3>


```json
{"gitdown": "gitinfo"}
```


[Gitinfo](https://github.com/gajus/gitinfo) gets info about the local GitHub repository.

<h4 id="features-gitinfo-example">Example</h4>


```json
{"gitdown": "gitinfo", "name": "username"}
{"gitdown": "gitinfo", "name": "name"}
{"gitdown": "gitinfo", "name": "url"}
{"gitdown": "gitinfo", "name": "branch"}
```


```
gajus
gitdown
https://github.com/gajus/gitdown
master
```

<h4 id="features-gitinfo-supported-properties">Supported Properties</h4>

|Name|Description|
|---|---|
|`username`|Username of the repository author.|
|`name`|Repository name.|
|`url`|Repository URL.|
|`branch`|Current branch name.|

<h4 id="features-gitinfo-json-configuration">JSON Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `name` | Name of the property. | N/A |

<h4 id="features-gitinfo-parser-configuration">Parser Configuration</h4>

| Name | Description | Default |
| --- | --- | --- |
| `gitinfo.gitPath` | Path to the `.git/` directory or a descendant. | `__dirname` of the script constructing an instance of `Gitdown`. |
