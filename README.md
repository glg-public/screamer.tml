# screamer.tml
A template PR screamer for github repos. This includes a basic check-runner framework with type definitions. It also includes some helpful utilities, and test coverage for those utilities.

- [screamer.tml](#screamertml)
  - [How To Use This Repo](#how-to-use-this-repo)
  - [Included Utilities](#included-utilities)
    - [camelCaseFileName](#camelcasefilename)
    - [clearPreviousRunComments](#clearpreviousruncomments)
    - [codeBlock](#codeblock)
    - [detectIndentation](#detectindentation)
    - [escapeRegExp](#escaperegexp)
    - [getAllRelevantFiles](#getallrelevantfiles)
    - [getContents](#getcontents)
    - [getLinesForJSON](#getlinesforjson)
    - [getLineNumber](#getlinenumber)
    - [getLineWithinObject](#getlinewithinobject)
    - [getNewIssueLink](#getnewissuelink)
    - [getNewFileLink](#getnewfilelink)
    - [getOwnerRepoBranch](#getownerrepobranch)
    - [httpGet](#httpget)
    - [leaveComment](#leavecomment)
    - [lineLink](#linelink)
    - [prLink](#prlink)
    - [suggest](#suggest)
    - [suggestBugReport](#suggestbugreport)

## How To Use This Repo

You will need to customize almost everything in this repo. It is not intended to be used "as is".

You can define your inputs in [action.yml](./action.yml), and then incorporate them into your screamer in [index.js:run](./index.js#L15-L21)

You can find a template check at [./checks/template.js](./checks/template.js).

Your checks get mounted in [./checks/index.js](./checks/index.js).

## Included Utilities

### camelCaseFileName

Takes a filename like secrets.json and returns secretsJson

```javascript
const { camelCaseFileName } = require('./util');

camelCaseFileName('secrets.json');
// secretsJson
```

### clearPreviousRunComments

```javascript
const { clearPreviousRunComments } = require('./util');
```

### codeBlock

```javascript
const { codeBlock } = require('./util');
```

### detectIndentation

```javascript
const { detectIndentation } = require('./util');
```

### escapeRegExp

```javascript
const { escapeRegExp } = require('./util');
```

### getAllRelevantFiles

```javascript
const { getAllRelevantFiles } = require('./util');
```

### getContents

```javascript
const { getContents } = require('./util');
```

### getLinesForJSON

```javascript
const { getLinesForJSON } = require('./util');
```

### getLineNumber

```javascript
const { getLineNumber } = require('./util');
```

### getLineWithinObject

```javascript
const { getLineWithinObject } = require('./util');
```

### getNewIssueLink

```javascript
const { getNewIssueLink } = require('./util');
```

### getNewFileLink

```javascript
const { getNewFileLink } = require('./util');
```

### getOwnerRepoBranch

```javascript
const { getOwnerRepoBranch } = require('./util');
```

### httpGet

```javascript
const { httpGet } = require('./util');
```

### leaveComment

```javascript
const { leaveComment } = require('./util');
```

### lineLink

```javascript
const { lineLink } = require('./util');
```

### prLink

```javascript
const { prLink } = require('./util');
```

### suggest

```javascript
const { suggest } = require('./util');
```

### suggestBugReport

```javascript
const { suggestBugReport } = require('./util');
```