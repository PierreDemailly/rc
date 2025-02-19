# rc
![version](https://img.shields.io/badge/dynamic/json.svg?url=https://raw.githubusercontent.com/NodeSecure/rc/master/package.json&query=$.version&label=Version)
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://github.com/NodeSecure/rc/commit-activity)
[![Security Responsible Disclosure](https://img.shields.io/badge/Security-Responsible%20Disclosure-yellow.svg)](https://github.com/nodejs/security-wg/blob/master/processes/responsible_disclosure_template.md
)
[![mit](https://img.shields.io/github/license/Naereen/StrapDown.js.svg)](https://github.com/NodeSecure/rc/blob/master/LICENSE)

NodeSecure runtime configuration.

## Requirements
- [Node.js](https://nodejs.org/en/) v16 or higher

## Getting Started

This package is available in the Node Package Repository and can be easily installed with [npm](https://docs.npmjs.com/getting-started/what-is-npm) or [yarn](https://yarnpkg.com).

```bash
$ npm i @nodesecure/rc
# or
$ yarn add @nodesecure/rc
```

## Usage example

Read:

```ts
import * as RC from "@nodesecure/rc";

const configurationPayload = (
  await RC.read(void 0, { createIfDoesNotExist: true })
).unwrap();
console.log(configurationPayload);
```

Write:
```ts
import assert from "node:assert/strict";
import * as RC from "@nodesecure/rc";

const writeOpts: RC.writeOptions = {
  payload: { version: "2.0.0" },
  partialUpdate: true
};

const result = (
  await RC.write(void 0, writeOpts)
).unwrap();
assert.strictEqual(result, void 0);
```

> 👀 .read and .write return Rust like [Result](https://doc.rust-lang.org/std/result/) object. Under the hood we use [ts-results](https://github.com/vultix/ts-results) to achieve this.

## API

> If `undefined` the location will be assigned to `process.cwd()`.

### read(location?: string, options?: readOptions): Promise< Result< RC, NodeJS.ErrnoException > >

```ts
interface createReadOptions {
  /**
   * If enabled the file will be created if it does not exist on the disk.
   *
   * @default false
   */
  createIfDoesNotExist?: boolean;
  /**
   * RC Generation mode. This option allows to generate a more or less complete configuration for some NodeSecure tools.
   *
   * @default `minimal`
   */
  createMode?: RCGenerationMode | RCGenerationMode[];
}

export type readOptions = RequireAtLeastOne<createReadOptions, "createIfDoesNotExist" | "createMode">;
```

The `createIfDoesNotExist` argument can be ignored if `createMode` is provided.

```ts
import * as RC from "@nodesecure/rc";

const configurationPayload = (
  await RC.read(void 0, { createMode: "ci" })
).unwrap();
console.log(configurationPayload);
```

### write(location?: string, options: writeOptions): Promise< Result< void, NodeJS.ErrnoException > >

By default the write API will overwrite the current payload with the provided one. When the `partialUpdate` option is enabled it will merge the new properties with the existing one.

```ts
/**
 * Overwrite the complete payload. partialUpdate property is mandatory.
 */
export interface writeCompletePayload {
  payload: RC;
  partialUpdate?: false;
}

/**
 * Partially update the payload. This implies not to rewrite the content of the file when enabled.
 **/
export interface writePartialPayload {
  payload: Partial<RC>;
  partialUpdate: true;
}

export type writeOptions = writeCompletePayload | writePartialPayload;
```

### CONSTANTS

```ts
import assert from "node:assert/strict";
import * as RC from "@nodesecure/rc";

assert.strictEqual(RC.CONSTANTS.CONFIGURATION_NAME, ".nodesecurerc");
```

### Generation Mode

We provide by default a configuration generation that we consider `minimal`. On the contrary, a `complete` value will indicate the generation with all possible default keys.

```ts
export type RCGenerationMode = "minimal" | "ci" | "report" | "complete";
```

However, depending on the NodeSecure tool you are working on, it can be interesting to generate a configuration with some property sets specific to your needs.

Note that you can combine several modes:

```ts
import * as RC from "@nodesecure/rc";

await RC.read(void 0, { createMode: ["ci", "report"] })
```

## JSON Schema

The runtime configuration is validated with a JSON Schema: `./src/schema/nodesecurerc.json`.

It can be retrieved by API if required:
```ts
import * as RC from "@nodesecure/rc";

console.log(RC.JSONSchema);
```

## Contributors ✨

<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
[![All Contributors](https://img.shields.io/badge/all_contributors-2-orange.svg?style=flat-square)](#contributors-)
<!-- ALL-CONTRIBUTORS-BADGE:END -->

Thanks goes to these wonderful people ([emoji key](https://allcontributors.org/docs/en/emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="https://www.linkedin.com/in/thomas-gentilhomme/"><img src="https://avatars.githubusercontent.com/u/4438263?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Gentilhomme</b></sub></a><br /><a href="https://github.com/NodeSecure/rc/commits?author=fraxken" title="Code">💻</a> <a href="https://github.com/NodeSecure/rc/issues?q=author%3Afraxken" title="Bug reports">🐛</a> <a href="https://github.com/NodeSecure/rc/pulls?q=is%3Apr+reviewed-by%3Afraxken" title="Reviewed Pull Requests">👀</a> <a href="https://github.com/NodeSecure/rc/commits?author=fraxken" title="Documentation">📖</a> <a href="#security-fraxken" title="Security">🛡️</a></td>
    <td align="center"><a href="https://dev.to/antoinecoulon"><img src="https://avatars.githubusercontent.com/u/43391199?v=4?s=100" width="100px;" alt=""/><br /><sub><b>Antoine Coulon</b></sub></a><br /><a href="https://github.com/NodeSecure/rc/commits?author=antoine-coulon" title="Code">💻</a> <a href="https://github.com/NodeSecure/rc/issues?q=author%3Aantoine-coulon" title="Bug reports">🐛</a> <a href="https://github.com/NodeSecure/rc/pulls?q=is%3Apr+reviewed-by%3Aantoine-coulon" title="Reviewed Pull Requests">👀</a></td>
  </tr>
</table>

<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->

<!-- ALL-CONTRIBUTORS-LIST:END -->

## License
MIT
