Modelgun 👾👾👾 🔫 - Data model and util auto-gen tool for Node.js

# Overview
This is a tool to generate model classes and related utils with validations.
Currently following files are generated.

- model: A class to represent a model which validation methods.
- parser: Parser to convert any object to models.
- faker (alpha): Fake value generation tool for tests.

## What is Modelgun🔫 originally?
Modelguns are Japanese replica or toy guns, which are usually made of zinc alloys or plastic materials.
https://en.wikipedia.org/wiki/Modelguns

# Motivation
We write data validation code so many times to build a system. But the work is
very boring, time consuming and can be done by robots. What human should do is
just design the structures of models and defines the validation rules.
This tool make you free from the boring works and your life will be easier 😃.

# How to

## 1. Install
npm
```
npm install --save-dev modelgun
```

yarn
```
yarn add --dev modelgun
```

## 2. Define models
Modelgun uses [toml](https://github.com/toml-lang/toml) as the format of
definition files. Use `[MODEL_NAME].model.toml` format for file names.
`[MODEL_NAME].model.ts`, `[MODEL_NAME].parser.ts` and `[MODEL_NAME].faker.ts` (alpha)
are generated for each model definition. See [Model definition file spec section](# Model definition file spec) for more details.

## Run
To run Modelgun generator, use following commands.
Modelgun processes definition files in a specified directory and generates files
in the same directory. (* Currently recursive checking of deep directories is not supported.)

In `scripts` in `package.json`, use
```
modelgun gen path/to/dir
```

To call script directory, use
```
./node_modules/modelgun/bin/cli.js gen path/to/dir
```

# Examples
Example model definition files and generated files are stored [here](https://github.com/ku6ryo/modelgun/tree/master/examples).

# Supported languages
Currently only TypeScript is supported. If number of users increase, will consider
to write JavaScript version.

# Model definition file spec

## Top level options
Model setting parameters.

### description
Description about the model.
```toml
description = "This is the data structure to "
```

### header
Header is placed at the top of auto-generated model file. You can write anything like constants, import statements and functions. This was introduced for custom validations.
But you may find more cases. Please let me know if you find some.
```toml
header = "
// Importing
import anyFunc from \"./path/to/file\"

function customValidator (value: string) {
  ...
}
"
```

## Generator
- parser: Whether to generate a parser file.
- faker: Whether to generate a faker file.
```toml
[generator]
parser = false
faker = true
```

## prop
`prop` Defines model properties. Property name follows after `prop`.

```toml
[props.name]
type = "string"
```

### type
Type of property. Please use supported types.
```toml
[props.str]
type = "int"
```

Supported types
- boolean
- float
- int
- string
- uuid
- url
- email

### null
Modelgun supports nullable for non-array properties. Specify `null` option like below.
```toml
[props.name]
type = "string"
null = true
```

#### Referring another model
Use "ref:[MODEL_NAME]"

TODO: Let users to use file path to a model.

### validation
Depending on types of properties, modelgun auto generates validation code in model class.
For some types, modelgun uses [validator.js](https://www.npmjs.com/package/validator). Please add the npm package when you use types which requires it (e.g. uuid, email).
Additionally, you can specify detailed validation.

#### Number
- min
- max
- candidates

#### String
- regex
- maxLength
- minLength
- candidates

#### Custom validator for any types
You can use your custom validator in files in your project. For those case, use
`header` to import your functions or write directly in the generated model file.
Throw `InvalidPropertyError` defined in the auto-generated model file for error
cases.

```toml
header = "
import nameValidator from \"./path/to/file\"

function ageValidator (value: number) {
  if (value === 1000) {
    InvalidPropertyError(\"1000 years old is unbelievable.\")
  }
}
"

[props.name]
type = "string"
customValidator = "nameValidator"

[props.age]
type = "number"
customValidator = "ageValidator"
```
