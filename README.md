# screamer.tml
A template PR screamer for github repos. This includes a basic check-runner framework with type definitions. It also includes some helpful utilities, and test coverage for those utilities.

## How To Use This Repo

You will need to customize almost everything in this repo. It is not intended to be used "as is".

You can define your inputs in [action.yml](./action.yml), and then incorporate them into your screamer in [index.js:run](./index.js#L15-L21)

You can find a template check at [./checks/templates.js](./checks/templates.js).

Your checks get mounted in [./checks/index.js](./checks/index.js).

