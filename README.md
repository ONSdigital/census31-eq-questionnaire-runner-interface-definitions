# Census 2031 EQ Questionnaire Runner Interface Definitions

This repository contains the documentation, definitions, and specifications for interfaces between EQ and the dependent services involved in the 2031 Census. It was cloned from the historical SDC repo https://github.com/ONSdigital/ons-schema-definitions for the purpose of clarity and ownership.

## Proposing Changes
1. Open a PR with proposed API changes
2. Add reviewers to the PR this change may affect
3. Changes should be agreed upon and approved between all affected teams
4. Any work that will come out of the proposal should be put on the teams backlogs

## Docs

Documentation can be found in `/docs`.

- [JSON Web Token (JWT) Profile](docs/jwt_profile.md)
- [EQ Launch - Upstream Respondent Management System to EQ Runner](docs/respondent_management_to_electronic_questionnaire_runner.md)
- [EQ Submission - EQ Runner to Downstream Ingestion Service](docs/electronic_questionnaire_runner_to_downstream.md)
- [JSON Examples](examples)

## JSON Schema Validation

The launch and submission JSON schema can be validated using JSON Schema definitions. The JSON schemas are defined using [Draft 2020-12](https://json-schema.org/specification-links.html#2020-12) and are validated via [AJV](https://ajv.js.org/). 

### Prerequisites
- Node installed matching the version specified in `.nvmrc`. It is recommended that you use [nvm](https://github.com/nvm-sh/nvm) to manage your Node versions.
- [NPM](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) (JS package and dependency manager)

**Install dependencies:**

```bash
npm install
```

**Validate all examples schemas**

```bash
./scripts/validate-schemas.js
```

**Validate a single file or folder**

```bash
./scripts/validate-schemas.js <schema-type> <schema-file-or-folder>
```

For example:
```bash
./scripts/validate-schemas.js submission_v2 examples/eq_runner_to_downstream/payload_v2/adhoc/
```

Help:
```bash
./scripts/validate-schemas.js --help
```


## Development

Format JSON/JS files
```bash
npm run format
```

Lint JSON/JS files
```bash
npm run lint
```
