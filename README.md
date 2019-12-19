# Welcome to wings4!

<p align="center">
  <img width="300" src="https://miro.medium.com/max/3728/1*7zccGWE4o5LmxegijjK_xQ.png"/>
  <br />
  <img width="300" src="https://feathersjs.com/img/feathers-logo-wide.png" />
</p>

A **FeathersJS 4-Way reactive data sync** for any frontend framework

 - [x] DOM / UI (HTML)
 - [x] Data / State (Javascript)
 - [x] Local Storage (Offline)
 - [x] Backend/Database (Cloud)

> **Join and support our Community** <br />
> Web and Mobile Developers PH <br />
> [ [Facebook Page](https://fb.com/webmobile.ph) | [Group](https://fb.com/groups/webmobile.ph/) ]

## Installation

```bash
npm install wings4
```
or
```bash
yarn add wings4
```

## Usage

```javascript
import wings from 'wings4'
// const wings = require('wings4').default

let app = wings('http://localhost:3030')

let messagesSrvc = app.wingsService('messages')

messagesSrvc.on('dataChange', (messages) => {
  console.log(messages)
})

messagesSrvc.init()
```
## app.wingsService(serviceName, params, config)
Returns a wingsService `<object>`

| Param| Type | Description |
|--|--|--|
| *serviceName* | `<string>` | Name of service |
| *params.query* | `<object>` | ( Optional ) Refer to  [Feathers Querying](https://docs.feathersjs.com/api/databases/querying) |
| *config* | `<object>` | ( Optional ) Configuration of wingsService `<object>` |
| *config.channels* | `<array>` | ( Optional ) Array of [channel](#channel) objects |

<span id="params"></span>
### *params.query* `<object>` 
Refer to [Feathers Querying](https://docs.feathersjs.com/api/databases/querying)
```javascript
/* example records
  [
    { text: 'Hello', read: true, roomId: 1, nested: { prop: 'xander' } },
    { text: 'World', read: false, roomId: 2, nested: { prop: 'ford' } }
  ]
*/

let serviceName = 'message'

let params = {
  query: {
    read: false,
    roomId: 2
  }
}

let messagesSrvc = app.wingsService(serviceName, params)

messagesSrvc.on('dataChange', (messages) => {
  console.log(messages)
  // [ { text: 'World', read: false, roomId: 2, nested: { prop: 'ford' } } ]
})
```

<span id="config"></span>
### *config* `<object>` 
| Property | Type | Default | Description |
|--|--|--|--|
| *debug* | `<boolean>` | false | Logs all events `init`, `created`, `removed`, `patched`, `updated`, `loadMore`, `reset`  |
| *newDataPosition* | `<string>` | 'end' | Add new items to the `start` or `end` of an array  |
| *paginate* | `<boolean>` | false | Enable pagination based on `$limit`.  ***default is 10 records per page* |
| *channels* | `<array>` | [] | Refer to [channels](#channel) |

```javascript
let config = {
  debug: true,
  newDataPosition: 'start',
  paginate: true,
  channels: []
} 

let messagesSrvc = app.wingsService(serviceName, params, config)
```

<span id="channel"></span>
### *config.channels* `<array>` and *channel* `<object>`
Channels determine which records to receive that passes the prop === value .

| Property | Type | Description |
|--|--|--|
| *prop* | `<string>` | Name of record's property |
| *value* | `<string | number | boolean | function>` | Equality test value |
| *value* | `<function>` | `callback` accepts `(val, message)` arguments for custom test. ***Must return a boolean value* |

```javascript
/* example records
  [
    { text: 'Hello', read: true, roomId: 1, nested: { prop: 'xander' } },
    { text: 'World', read: false, roomId: 2, nested: { prop: 'ford' } }
  ]
*/

let config = {
  channels: [
    { prop: 'roomId', value: 2},
    { prop: 'nested.prop', value: 'ford'},
    { prop: 'nested', value: (val) => val.prop === 'ford' }
  ]
}

let messagesSrvc = app.wingsService(serviceName, params, config)
```

> You may use dot notation in **prop** as reference into the object's property

## reset(params, config)
set new `params`, `config` and triggers `init` method
```javascript
let params = {
  query: {
    read: false,
    roomId: 2
  }
}

let config = {
  debug: true,
  newDataPosition: 'start',
  paginate: true,
  channels: []
}

messagesSrvc.reset(params, config)
``` 

## loadMore()
loads more data based on `$skip` = ( `page` + 1 ) * `$limit`  
```javascript
messagesSrvc.loadMore()
``` 

## loadAll()
loads all data based on (`$skip` = `page` * `$limit`) * `pages`
```javascript
messagesSrvc.loadAll()
``` 

## loadPage(page)
loads the based on  `$skip` = `page` * `$limit`
```javascript
messagesSrvc.loadPage(2)
```

## destroy()
destroys all listners created by `.on(eventName, listener)` function
```javascript
messagesSrvc.destroy()
```

## Single Sign-On (Backend)
```bash
npm install feathers-sso
```
or
```bash
yarn add feathers-sso
```

```javascript
// authentication.js
const { LocalStrategy } = require('@feathersjs/authentication-local');
const { expressOauth } = require('@feathersjs/authentication-oauth');
const Sso = require('feathers-sso')

module.exports = app => {
  const authentication = new AuthenticationService(app);

  authentication.register('jwt', new JWTStrategy());
  authentication.register('local', new LocalStrategy());
  authentication.register('sso', new Sso('http://localhost:3030'));

  app.use('/authentication', authentication);
  app.configure(expressOauth());
};
```

## Single Sign-On (Frontend)
enables SSO to specific iframe parent host
```javascript
// SSO login UI
app.enableSSO([
  'http://localhost:8080', 
  'http://localhost:3030'
])
```
authenticate using SSO specifying the URL of SSO login UI
```javascript
// Connecting Client App to SSO login UI
app.authenticateSSO('http://localhost:8080/#/auth')
```

# *Join and support our Community* <br /> **Web and Mobile Developers PH** <br/> [ [Facebook Page](https://fb.com/webmobile.ph) | [Group](https://fb.com/groups/webmobile.ph/) ]

