[![Travis](https://img.shields.io/travis/zhorton34/vuejs-form/master.svg?style=flat-square)](https://travis-ci.org/zhorton34/vuejs-form/builds)
[![npm downloads](https://img.shields.io/npm/dm/vuejs-form.svg?style=flat-square)](http://badge.fury.io/js/vuejs-form)
[![npm license](https://img.shields.io/npm/l/vuejs-form.svg?style=flat-square)](http://badge.fury.io/js/vuejs-form)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![dependencies](https://img.shields.io/badge/dependencies-none-brightgreen.svg?style=flat-square)](https://github.com/zhorton34/vuejs-form/blob/master/package.json)
[![npm version](https://img.shields.io/npm/v/vuejs-form.svg?style=flat-square)](http://badge.fury.io/js/vuejs-form)


# That Vue Form (Simplified)

> Convenient Wrapper For Form Input Data (Dependency Free ~ Usable Outside Externally From Vue)


### Installation

#### NPM

```bash
npm install --save-dev vuejs-form
```

#### Yarn

```bash
yarn add vuejs-form --save
```

#### Code Pen (Real Time Errors Example)

- [CodePen (VueJS Form & VueJS Validators Example)](https://codepen.io/zhorton34/pen/zYvWZYz)


### VueJS Form Can Comes With VueJS Validators Rules
- [(npm)](https://www.npmjs.com/package/vuejs-validators)
- [(github)](https://github.com/zhorton34/vuejs-validators)
- _Fast_ Setup
- _Zero_ Dependencies
- _Tested_ Thoroughly
- _Simplified_ Syntax
- _Extremely_ Lightweight
- _Simplified_ Extendability
_Did You Know? Individually, each package has ZERO Non-Dev Dependencies & can be used independently, but ultimately were built in parallel with each other._

```js
<template>
    <div>
        <div v-if="form.errors().any()" v-for="(message, key) in form.errors().list()" :key="`${key}.error`">
            {{ message }}
        </div>

        <input type='text' v-model='form.name' /> <br>
        <input type='email' v-model='form.email' /> <br>
        <input type='password' v-model='form.password' /> <br>
        <input type='password' v-model='form.confirm_password' /> <br>
        <hr>
        <button :disabled='form.empty()' @click='submit'>
            Complete
        </button>
    </div>
</template>

<script>
import form from 'vuejs-form'

export default {
    data: () => ({
        form: form({
            email: '',
            password: '',
            confirm_password: ''
        })
        .rules({
            email: 'email|min:5|required',
            password: 'same:confirm_password',
            confirm_password: 'min:6|required',
        })
        .messages({
            'email.required': ':attribute is required',
            'email.email': ':attribute must be a valid email',
            'email.min': ':attribute may not have less than :min characters',
            'password.same': 'Whoops, :attribute does not match the :same field',
        }),
   }),

   watch: {
       /*--------------------------------------------------------------
        * When Should Your Form "Validate", Providing Error Messages?
        *--------------------------------------------------------------
        * Form validates every time form data is updated. To
        * display errors on form submit, remove watcher &
        * move "this.form.validate()" over to submit()
        *--------------------------------------------------------------
        */
        ['form.data']: {
            deep: true,
            immediate: false,
            handler: (now, old) => { this.form.validate(); },
        }
   },

    methods: {
        failed() {
            console.log('errors: ', this.form.errors().all());
        },
        passed() {
            console.log('data: ', this.form.all());
            console.log('wrapped data: ', this.form.wrap('data'));
        },
        submit() {
            return this.form.errors().any() ? this.failed() : this.passed();
        },
    }
}
</script>
```


### API

All available methods

- [all](#all)
- [boolean](#boolean)
- [empty](#empty)
- [except](#except)
- [fill](#fill)
- [filled](#filled)
- [forget](#forget)
- [has](#has)
- [hasAny](#hasany)
- [input](#input)
- [keys](#keys)
- [macro](#macro)
- [make](#make)
- [missing](#missing)
- [only](#only)
- [set](#set)
- [toArray](#toarray)
- [wrap](#wrap)

#### `all()`

The all method returns the underlying input object represented by the form:

```js
form({ name: 'sarah', email: 'sarah@gmail.com' }).all();

// { name: 'sarah', email: 'sarah@gmail.com' }
```

#### `boolean(property)`

The boolean method determines if the given field has a truthy or falsy values:
#### Truthy values: true, "true", "yes", "on", "1", 1
#### Falsy values: Everything else

```js

const LoginForm = form({
    name: '',
    email: '',
    terms: ''
})

LoginForm.terms = true
LoginForm.boolean('terms') // true

LoginForm.terms = 'true'
LoginForm.boolean('terms') // true

LoginForm.terms = 'yes'
LoginForm.boolean('terms') // true

LoginForm.terms = 'on'
LoginForm.boolean('terms') // true

LoginForm.terms = "1"
LoginForm.boolean('terms') // true

LoginForm.terms = 1
LoginForm.boolean('terms') // true
```


#### `empty(one, two, three, ...)`

The empty method determines if the input property exists but the value is empty:

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.empty('name') // false
ExampleForm.empty('name', 'email') // false

ExampleForm.empty('id') // true
```

#### `except(one, two, three, ...)`

The except method grabs all of the inputs except the properties passed in:

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.except('id')
/**
 * { name: 'sarah', email: 'sarah@gmail.com' }
*/

ExampleForm.except('id', 'name')
/**
 * { email: 'sarah@gmail.com' }
 */
```
[View source on GitHub](https://github.com/zhorton34/vuejs-form.js/blob/master/src/methods/except.js)

#### `fill({ key: value, keyTwo: valueTwo, etc... })`

The fill method allows you to fill in new or empty values without overriding existing values:

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.fill({
    id: 2,
    name: 'tim',
    email: 'tim@gmail.com'
})

ExampleForm.all()
// { id: 2, name: 'sarah', email: 'sarah@gmail.com' }
```

#### `filled(propertyOne, propertyTwo, etc...)`

The filled method determine if a value is filled (AKA not empty):

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.filled('id', 'name') // false
ExampleForm.filled('name', 'email') // true
```

#### `forget(propertyOne, propertyTwo, etc...)`

The forget method will remove or "forget" a key value pair from the form input data

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.forget('id', 'name')
ExampleForm.all() // { email: 'sarah@gmail.com' }
```

#### `has(propertyOne, propertyTwo, etc...)`

The has method will determine if a key exists within the form input data

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.has('id', 'name') // true
ExampleForm.has('something', 'id', 'name') // false
```

#### `hasAny(propertyOne, propertyTwo, etc...)`

The hasAny method will determine if a key has any of the given properties within the form input data

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.hasAny('id', 'name') // true
ExampleForm.hasAny('something', 'id', 'name') // true
```

#### `input(property, default = false)`

The input method will resolve a given input value or default to false. You can define a default as the second parameter

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.input('id') // false
ExampleForm.input('id', 1) // 1
ExampleForm.input('name', 'tim') // sarah
```

#### `keys()`

The keys method will resolve an array of the input keys

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.keys() // ['id', 'name', 'email']
```


#### `macro(key, fn)`

The macro method can be used to extend upon the form object:

```js
import form from 'vuejs-form';

form().macro('count', () => {
    return this.keys().length;
});

form().macro('mapInto', into => {
    return this.toArray().reduce((accumulated, { key, value }) => ({
            ...accumulated,
            ...into(key, value)
        }),
    {});
});

const ExampleForm = form({
    email: 'example@gmail',
    password: 'secret',
});

ExampleForm.mapInto((key, value) => ({ [`example_form_${key}`]: value }));
// { example_form_email: 'example@gmail.com', 'example_form_password': 'secret' };

```

[View source on GitHub](https://github.com/zhorton34/vuejs-form.js/blob/master/src/methods/extend.js)

#### `make({ ... })`

The make method will "make" a new form when used on the underlying class (With the proxy used on all forms)

```js
import { VueForm } from 'vuejs-form'

const ExampleForm = VueForm.make({ id: '', name: 'sarah', email: 'sarah@gmail.com' })
ExampleForm.all() // { id: '', name: 'sarah', email: 'sarah@gmail.com' }
```


#### `missing(propertyOne, propertyTwo, ...)`

The missing method will determine if the form is missing the following properties

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' })

ExampleForm.missing('id') // false
ExampleForm.missing('something') // true
ExampleForm.missing('name', 'email') // false
ExampleForm.missing('name', 'email', 'something') // true
```


#### `only(propertyOne, propertyTwo, ...)`

The only method will return an object of "only" the input properties you defined

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' })

ExampleForm.only('name', 'email') // { name: 'sarah', email: 'sarah@gmail.com' }
ExampleForm.only('id', 'name') // { id: '', name: 'sarah' }
ExampleForm.only('id') // { id: '' }
```


#### `set({ key: value, keyTwo: valueTwo, etc... })`

The set method allows you to set new and override previous values:

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.set({
    id: 2,
    name: 'tim',
    email: 'tim@gmail.com',
    password: 'secret',
})

ExampleForm.all()
// { id: 2, name: 'tim', email: 'tim@gmail.com', password: 'secret' }
```

#### `toArray()`

The toArray method transforms the input into an array of key value pair objects:

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.toArray()
/**
    [
        { key: 'id', value: '' },
        { key: 'name', value: 'sarah' },
        { key: 'email', value: 'sarah@gmail.com' }
    ]
*/

```

#### `wrap(key)`

The wrap method allows you to wrap the input within a given object key:

```js
const ExampleForm = form({ id: '', name: 'sarah', email: 'sarah@gmail.com' });

ExampleForm.wrap('data')
/**
  {
    data: {
        id: '',
        name: 'sarah',
        email: 'sarah@gmail.com'
    }
  }
*/

```

#### Utilization

```js
import form from 'vuejs-form'

const LoginForm = form({
    name: '',
    email: '',
    password: '',
})

LoginForm.name // ''
LoginForm.name = 'sarah'
LoginForm.name // 'sarah'

form({
    name: '',
    email: '',
    password: '',
}).all() // { name: 'sarah', email: '', password: '' }
form({
    name: '',
    email: '',
    password: '',
}).has('email', 'password') // true
form({
    name: '',
    email: '',
    password: '',
}).has('email', 'something') // false
form({
    name: '',
    email: '',
    password: '',
}).hasAny('email', 'something') // true
form({
    name: '',
    email: '',
    password: '',
}).empty('email') // true
form({
    name: '',
    email: '',
    password: '',
}).filled('email') // false
form({
    name: '',
    email: '',
    password: '',
}).filled('name') // true
form({
    name: '',
    email: '',
    password: '',
}).boolean('email') // false
form({
    name: '',
    email: '',
    password: '',
}).only('email', 'name') // { email: '', name: '', }
form({
    name: '',
    email: '',
    password: '',
}).except('password') // { email: '', name: '' }
form({
    name: '',
    email: '',
    password: '',
}).input('password') // ''
form({
    name: '',
    email: '',
    password: '',
}).input('email', 'example@gmail.com') // 'example@gmail.com'

LoginForm.fill({
    name: 'tim',
    email: 'tim@gmail.com',
    password: 'secret'
})

LoginForm.all() // { name: 'sarah', email: 'tim@gmail.com', password: 'secret' }

LoginForm.set({
    name: 'jamie',
    email: 'jamie@gmail.com',
    password: 'password'
})

LoginForm.all() // { name: 'jamie', email: 'tim@gmail.com', password: 'secret' }

LoginForm.keys() // ['name', 'email', 'password']

LoginForm.missing('verified') // true
LoginForm.missing('email') // false

LoginForm.toArray()
/**
 [
    { key: 'name', value: 'jamie' },
    { key: 'email', value: 'tim@gmail.com' },
    { key: 'password', value: 'secret' }
 ]
*/

LoginForm.wrap('data')
/**
{
    data: {
        name: 'jamie',
        email: 'tim@gmail.com',
        password: 'secret'
    }
}
*/

LoginForm.forget('password', 'email')
LoginForm.all() // { name: 'jamie' }

/**
 * When dealing with HTML elements like checkboxes, your application may receive "truthy" values that are actually strings. For example, "true" or "on". For convenience, you may use the boolean method to retrieve these values as booleans. The boolean method returns true for 1, "1", true, "true", "on", and "yes". All other values will return false:
 *   Boolean checks for
*/
LoginForm.boolean('name') // false


LoginForm.terms = true
LoginForm.boolean('terms') // true
LoginForm.terms = 'true'
LoginForm.boolean('terms') // true
LoginForm.terms = 'yes'
LoginForm.boolean('terms') // true
LoginForm.terms = 'on'
LoginForm.boolean('terms') // true
LoginForm.terms = "1"
LoginForm.boolean('terms') // true
LoginForm.terms = 1
LoginForm.boolean('terms') // true

/** Anything else will return false Ex: */
LoginForm.terms = 'asdfsdf'
LoginForm.boolean('terms') // false
```

#### Extend Form Functionality
```js
import form from 'vuejs-form'

form().macro('count', () => {
    return this.keys().length
})

form().macro('mapInto', into => {
    // NOTICE: this.data is where the input object is actually stored

    this.data = Object.entries(this.data).reduce((input, [key, value]) => ({
            ...input,
            ...into(key, value)
        },
    {})

    return this
})



const extendedForm = form({
    email: 'example@gmail',
    password: 'secret',
})

form().macro((key, value) => ({ [key]: value.split('@') })).all()
/**
 * { email: ['example', 'gmail'], password: 'secret' }
 */
```






### Contribute

PRs are welcomed to this project. 
If you want to improve the vuejs-form library, add 
functionality or improve the docs please feel free to submit a PR.

### License

MIT © [Zachary Horton (Clean Code Studio)](https://www.youtube.com/channel/UCq0m4ebGqurYQLwD-1aYsvg)

## Change Log

### 1.1.1
- "form.getErrors()" replaced with "form.errors()"
- "form.getValidator()" replaced with "form.validator()"
- "vuejs-validators" setup as dev dependency
- "ValidatableForm" Export ~ (Ex: const { ValidatableForm } = require('vuejs-form'))
- Default import is ValidatableForm (Ex: import form from 'vuejs-form' has validator || import { form } from 'vuejs-form' does not have validator)
