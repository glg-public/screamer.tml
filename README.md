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

Clear any comments from this bot that are already on the PR.
This prevents excessive comment polution

```javascript
const { clearPreviousRunComments } = require('./util');

await clearPreviousRunComments(octokit, { owner, repo, pull_number });
```

### codeBlock

Formats text as a markdown code block.

```javascript
const { codeBlock } = require('./util');

codeBlock(
  JSON.stringify({ key: "value"}, null, 2), 
  'json'
);
```

Outputs: 

    ```json
    {
      "key": "value"
    }
    ```



### detectIndentation

Determines whether a file uses tabs or spaces, and how many. If a file uses inconsistent indentation, it will return the most common form. This was written for JSON files, but should work with any consistently indented file.

```javascript
const { detectIndentation } = require('./util');

detectIndentation(fileLines);
// { amount: 2, type: 'spaces', indent: '  '}
```

### escapeRegExp

Escapes a string so that it can be matched literally as a regex.
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions#Escaping

```javascript
const { escapeRegExp } = require('./util');

escapeRegExp("test[1-9]+");
// test\[1\-9\]\+
```

### getAllRelevantFiles

Takes an array of GitHub Files (as returned by GitHub API), and returns an array of Directory Objects, ready to have checks run on them.

```javascript
const { getAllRelevantFiles } = require('./util');

const { data: files } = await octokit.pulls.listFiles({
  owner,
  repo,
  pull_number,
});

// This should be a list of files you want to scream at
const filesToCheck = [
  'security.json',
  'readme',
  'orders'
];

const dirsTocheck = await getAllRelevantFiles(files, filesToCheck);
```

### getContents

Read relevant files from the directory,
and split them by `\n`.

```javascript
const { getContents } = require('./util');

const directory = await getContents('streamliner', ['orders', 'secrets.json'])
```

Returns

```javascript
{
  directory: 'streamliner',
  ordersPath: 'streamliner/orders',
  ordersContents: [
    'export CAT="pants"',
    'dockerdeploy github/glg/someapp/main:latest'
  ],
  secretsJsonPath: 'streamliner/secrets.json',
  secretsJsonContents: [
    '[',
    '  {',
    '    "name": "something",',
    '    "valueFrom": "arn:something:secret"',
    '  }',
    ']'
  ]
}
```

### getLinesForJSON

Identifies the start and end line for a JSON object in a file

```javascript
const { getLinesForJSON } = require('./util');

let fileLines = [
"[",
"  {",
'    "name":"WRONG",',
'    "valueFrom": "differentarn"',
"  },",
"  {",
'    "name":"MY_SECRET",',
'    "valueFrom": "arn"',
"  }",
"]",
];

let jsonObj = { name: "MY_SECRET", valueFrom: "arn" };
let lines = getLinesForJSON(fileLines, jsonObj);
// { start: 6, end: 9 }
```

### getLineNumber

Returns the first line number that matches a given RegExp.
Returns null if no lines match

```javascript
const { getLineNumber } = require('./util');

const ordersContents = [
  "export SOMETHING=allowed",
  'export SOMETHING_ELSE="also allowed"',
];

const regex = /export SOMETHING_ELSE=/;
const line = getLineNumber(ordersContents, regex);
// 2
```

### getLineWithinObject

Looks for a line that matches a given RegExp, and is also within a specified object.

```javascript
const { getLineWithinObject } = require('./util');

const secretsJson = [
  {
    name: "JWT_SECRET",
    valueFrom: "some secret arn",
  },
];
const secretsJsonContents = JSON.stringify(secretsJson, null, 2).split("\n");
const regex = new RegExp(`"name":\\s*"${secretsJson[0].name}"`);
const lineNumber = getLineWithinObject(
  secretsJsonContents,
  secretsJson[0],
  regex
);
// 3
```

### getNewIssueLink

Generates a markdown link that creates a new issue on a specified github repository

```javascript
const { getNewIssueLink } = require('./util');

const issueLink = getNewIssueLink({
  linkText: "Create an issue",
  owner: "glg-public",
  repo: "screamer.tml",
  title: "Test Error",
  body: "This text will be in the body of the issue",
});

// [Create an issue](https://github.com/glg-public/screamer.tml/issues/new?title=Test%20Error&body=This%20text%20will%20be%20in%20the%20body%20of%20the%20issue)
```

### getNewFileLink

Creates a url that proposes a new file in github

```javascript
const { getNewFileLink } = require('./util');

const link = getNewFileLink({
  owner: "glg-public",
  repo: "screamer.tml",
  branch: "main",
  filename: "test/fixtures/new-fixture.json",
  value: JSON.stringify(
    {
      key: "value",
    },
    null,
    2
  ),
});

// https://github.com/glg-public/screamer.tml/new/main?filename=test%2Ffixtures%2Fnew-fixture.json&value=%7B%0A%20%20%22key%22%3A%20%22value%22%0A%7D
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