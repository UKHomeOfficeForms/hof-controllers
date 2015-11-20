# hof-controllers [![npm version](https://badge.fury.io/js/hof-controllers.svg)](https://badge.fury.io/js/hof-controllers) [![Build Status](https://travis-ci.org/UKHomeOffice/hof-controllers.svg)](https://travis-ci.org/UKHomeOffice/hof-controllers)

A collection of controllers extended from [passports-form-wizard](https://github.com/UKHomeOffice/passports-form-wizard) Wizard, Form Controller:
```js
require('hmpo-form-wizard').Controller
```

## Usage

```js
var controllers = require('hof-controllers');
```

### Base Controller

Accessed as `base` from `hof-controllers`

```js
var baseController = require('hof-controllers').base;
```

Extends from [passports-form-wizard](https://github.com/UKHomeOffice/passports-form-wizard) Wizard, Form Controller.

#### Added functionality for clearing sessions

```js
{
  clearSession: true,
  /* step options */
}
```
#### Handles edit actions.

In the wizard options

```js
  hofWizard(steps, fields, {
    /* wizard options */
    params: '/:action?'
  });
```

In the view template

```js
a href='page_name/edit'
```

Or override in step options

```js
{
  continueOnEdit: true
  /* step options */
}
```

#### Locals for pluralisation

Adds `single` or `multiple` to the locals to describe the number of errors for pluralisation of error messages.

#### Handles journey forking

Each step definition accepts a `next` property, the value of which is the next route in the journey. By default, when the form is successfully submitted, the next steps will load. However, there are times when it is necessary to fork from the current journey based on a users response to certain questions in a form. For such circumstances there exists the `forks` property.

In this example, when the submits the form, if the field called 'example-radio' has the value 'superman', the page at '/fork-page' will load, otherwise '/next-page' will be loaded.

```js

'/my-page': {
  next: '/next-page',
  forks: [{
    target: '/fork-page',
    condition: {
      field: 'example-radio',
      value: 'superman'
    }
  }]
}
```

The condition property can also take a function. In the following example, if the field called 'name' is more than 30 characters in length, the page at '/fork-page' will be loaded.

```js

'/my-page': {
  next: '/next-page',
  forks: [{
    target: '/fork-page',
    condition: function (req, res) {
      return req.form.values['name'].length > 30;
    }
  }]
}
```

Forks is an array and therefore each fork is interrogated in order from top to bottom. The last fork whose condition is met will assign its target to the next page variable.

In this example, if the last condition resolves to true - even if the others also resolve to true - then the page at '/fork-page-three' will be loaded. The last condition to be met is always the fork used to determine the next step.

```js

'/my-page': {
  next: '/next-page',
  forks: [{
    target: '/fork-page-one',
    condition: function (req, res) {
      return req.form.values['name'].length > 30;
    }
  }, {
    target: '/fork-page-two',
    condition: {
      field: 'example-radio',
      value: 'superman'
    }
  }, {
    target: '/fork-page-three',
    condition: function (req, res) {
      return typeof req.form.values['email'] === 'undefined';
    }
  }]
}
```

--------------------------------

### Date Controller

Accessed as `date` from `hof-controllers`

```js
var dateController = require('hof-controllers').date;
```

Extends from `require('hof-controllers').base;`

#### Date validation

- Validates the dates as a single item.

- Date validators default to: `required`, `numeric`, `format` (`DD-MM-YYYY`), and `future`.

- What the validators the date validates against can be overridden with the `validate` property on the date key field.

In this example, the whole date will only validate if it contains non-numeric characters.

```js
{
  date: {
    validate: ['numeric']
  }
}
```

Note: In the preceding example the field is not required and will not error on empty values.


#### Extend and override `validateField`

If you want a shared date field to be required, but on a particular page wish it to be optional, `validateField` will accept a third parameter called `isRequired`.
This will allow the date field to be optional unless the user enters a value, in which case an appropriate message will be shown.

```js
MyController.prototype.validateField = function validateField(keyToValidate, req) {
  return DateController.prototype.validateField.call(this, keyToValidate, req, false);
};
```

#### Formats date

- Adds a 'pretty' formatted (`D MMMM YYYY`) date to the form values.

------------------------------

### Error Controller

A simple wrapper around `require('hmpo-form-wizard').Error;` to make it easier to extend and customise error behaviour on error.

### Extending

To extend the functionality of a controller call the parent constructor and use node `util` to inherit the prototype;

```js
var DateController = function DateController() {
  Controller.apply(this, arguments);
};

util.inherits(DateController, Controller);
```
------------------------------

## Test

```bash
$ npm test
```

