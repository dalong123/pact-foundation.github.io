# Mocha / Node

### Mocha
If you starting a new project, run `npm init` to get a `package.json` going.

With a `package.json` file in place run `npm install --save-dev --save-exact mocha chai` on your terminal to install [Mocha](https://mochajs.org/) and [Chai.js assertion library](http://chaijs.com/).

Once you're ready to go with mocha install the DSL by running `npm install --save-dev --save-exact pact-js-mocha`.

#### Test
Write your Mocha test like below:

```javascript
var expect = require('chai').expect
// using superagent-bluebird-promise for request
// but you can use whatever you want
var request = require('superagent-bluebird-promise')

var PROVIDER_URL = 'http://localhost:1234'

Pact('PactUI', 'Projects Provider', PROVIDER_URL, function () {

  var EXPECTED_BODY = [{
    id: 1,
    name: 'Project 1',
    due: '2016-02-11T09:46:56.023Z',
    tasks: [
      {id: 1, name: 'Do the laundry', 'done': true},
      {id: 2, name: 'Do the dishes', 'done': false},
      {id: 3, name: 'Do the backyard', 'done': false},
      {id: 4, name: 'Do nothing', 'done': false}
    ]
  }]

  add(function (interaction) {
    interaction
      .given('i have a list of projects')
      .uponReceiving('a request for projects')
      .withRequest('get', '/projects', null, { 'Accept': 'application/json' })
      .willRespondWith(200, { 'Content-Type': 'application/json; charset=utf-8' }, EXPECTED_BODY)
  })

  function requestProjects () {
    return request.get(PROVIDER_URL + '/projects').set({ 'Accept': 'application/json' })
  }

  verify('single interaction', requestProjects, function (result, done) {
    expect(JSON.parse(result)).to.eql(EXPECTED_BODY)
    done()
  })

})
```

#### Running it
If you noticed it, the test looks a bit different. Instead of the normal `describe` block you now have a `Pact` block with a `verify` in place of `it` test function.

Since we are using Pact JS Mocha, you have to tell Mocha to use the inteface. To do that you can create a `mocha.opts` file on your test folder and tell Mocha to use it, like this:

```
--require ./node_modules/pact-js-mocha/src/index.js
```

You will also need to create two folders:
* `logs` where you can check all the interactions received by the Mock Service
* `pacts` where it will store all `Pacts` successfully verified by the test

From there, type in `mocha` on your terminal to get your test executed. Once successful a new Pact file will be generated at `pacts/pactui-projects_provider.json` that looks somewhat like this:

```json
{
  "consumer": {
    "name": "PactUI"
  },
  "provider": {
    "name": "Projects Provider"
  },
  "interactions": [
    {
      "description": "a request for projects",
      "provider_state": "i have a list of projects",
      "request": {
        "method": "GET",
        "path": "/projects",
        "headers": {
          "Accept": "application/json"
        }
      },
      "response": {
        "status": 200,
        "headers": {
          "Content-Type": "application/json; charset=utf-8"
        },
        "body": [
          {
            "id": 1,
            "name": "Project 1",
            "due": "2016-02-11T09:46:56.023Z",
            "tasks": [
              {
                "id": 1,
                "name": "Do the laundry",
                "done": true
              },
              {
                "id": 2,
                "name": "Do the dishes",
                "done": false
              },
              {
                "id": 3,
                "name": "Do the backyard",
                "done": false
              },
              {
                "id": 4,
                "name": "Do nothing",
                "done": false
              }
            ]
          }
        ]
      }
    }
  ],
  "metadata": {
    "pactSpecificationVersion": "2.0.0"
  }
}
```
