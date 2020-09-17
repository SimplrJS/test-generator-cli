<div align="center">
  <a href="https://github.com/SimplrJS">
    <img width="200" src="https://user-images.githubusercontent.com/7989797/27446299-b93aa76c-5785-11e7-8ef6-475f858e3291.png" />
  </a>
</div>
<h1 align="center">test-generator-cli</h1>

Tool to generate tests from cases.

## Installation

```sh
npm install @simplrjs/test-generator-cli -g
```

Global installation is not necessary. You can install this package with:

```sh
npm install @simplrjs/test-generator-cli --save-dev
```

and use it with [`npm-scripts`](https://docs.npmjs.com/misc/scripts).

## Command line

### Usage

```sh
test-generator-cli -h
```

### Arguments

| Argument                      | Type      | Default                   | Description                                                                   |
|-------------------------------|-----------|---------------------------|-------------------------------------------------------------------------------|
| -h, --help                    | boolean   | `false`                   | Show help.                                                                    |
| -v, --version                 | boolean   | `false`                   | Show current version.                                                         |
| -p, --project                 | string    | `./`                      | Project directory path.                                                       |

### Project tests structure

Let's say you picked `src` as your project directory.

```bash
├── src
    └── tests
        ├── cases
        │   ├── __tests__
        │   |   └── __snapshots__
        │   ├── case-1
        │   │   ├── ...
        │   │   ├── test-config.json
        │   │   └── case.test.tpl
        │   └── case-2
        └── default.test.tpl
```

| File / Directory          | Description                                                                                                                                                     |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `__tests__`               | Generated tests output directory.                                                                                                                               |
| `__snapshots__`           | Generated tests snapshots directory.                                                                                                                            |
| `case-1`, `case-2`        | Directories for test cases.                                                                                                                                     |
| `case.test.tpl`           | Test template file for certain test case. It's not required. If `case.test.tpl` not found in case directory, test template will fallback to `default.test.tpl`. |
| `test-config.json`        | Config file for a test case. You can reach it template. Config structure is at your discretion.                                                                 |
| `default.test.tpl`        | Default test template file.                                                                                                                                     |

### Templates

Package supports two kinds of templates:

* `case.test.tpl` - test template file for certain test case. It's not required. If `case.test.tpl` not found in case directory, test template will fallback to `default.test.tpl`.
* `default.test.tpl` - default test template file.

Template should be a valid TypeScript file that can have [Handlebars](http://handlebarsjs.com/) expressions.

#### Supported expressions

| Expression              | Description                                           |
|-------------------------|-------------------------------------------------------|
| `{{caseName}}`          | Test case name.                                       |
| `{{projectDirectory}}`  | Project directory path.                               |
| `{{json testConfig}}`   | Test case config file (`test-config.json`) content.   |

#### Sample

```typescript
import * as path from "path";
import { Bundler } from "@src/bundler";

test("{{caseName}}", async done => {
    const projectDirectory = "{{projectDirectory}}";
    const testConfig = {{{json testConfig}}};
    const entryFile = path.join(projectDirectory, testConfig.Entry);

    try {
        const bundleResult = await new Bundler()
            .BundleAll([entryFile]);
        expect(bundleResult[0].bundledContent).toMatchSnapshot();
        done();
    } catch (error) {
        done.fail(error);
    }
});
```

## License

Released under the [MIT license](LICENSE).
