# Provider
In order to verify provider interactions against a `Pact` file created by a consumer you can rely on [Pact JS Mocha](https://github.com/pact-foundation/pact-js-mocha) to get the job done.

Pact JS Mocha provides an interface that extends Mocha's own [BDD interface](https://mochajs.org/#bdd) with all the original hooks still working as expected.

Once the library is installed you have to tell Mocha to use it. To do that you can either create a `mocha.opts` file on your test folder with the line below or pass it as an argument on the command line:
```
--require ./node_modules/pact-js-mocha/src/index.js
```
Then all you have to do is write your test like below (make sure you have your provider service running):

```javascript
var expect = require('chai').expect

PactProvider('Projects Consumer', 'Projects Provider', function () {

  var pactOpts = {
    providerBaseUrl: 'http://my.provider.com',
    pactUrls: '/path/to/pact_file.json',
    providerStatesUrl: 'http://my.provider.com/providerStates',
    providerStatesSetupUrl: 'http://my.provider.com/setupProviderStates',
  }

  honourPact(pactOpts, function (result, done) {
    done()
  })

})
```
