# angular-e2e
Node image with Google Chrome and Xvfb prepared for Angular e2e tests

## Usage ##

From the home of the Angular project to be tested:

```sh
docker run -d -v $(pwd):/workdir --name angular-e2e-container rogargon/angular-e2e
```

This starts a container based on Node that includes Google Chrome and Xvfb and mounts in /workdir the Angular project to be tested. 

If the project already includes "/node_modules", and they can be used from the Node image which is based on Debian Linux, there is no need to reinstall them as they are also mounted in the container. Otherwise, run:

```sh
docker exec angular-e2e-container npm install
```

Then, it is possible to start the Angular application running:

```sh
docker exec angular-e2e-container npm run start &
```

Finally, run the e2e tests against the running Angular application:

```sh
docker exec angular-e2e-container npm run e2e
```

### Protractor and Karma Configuration ###

Projects generated using angular-cli use Protactor for e2e tests. It is configured in the file "protractor.conf.js". In order to work with a Google Chrome running in a container, it should be configured as follows:

```javascript
  ...
  capabilities: {
    'browserName': 'chrome',
    'chromeOptions': {
      'args': ['no-sandbox']
    }
  },
  ...
```

For Karma tests, the configuration is provided in "karma.conf.js". In this case the configuration should be:

```javascript
  ...
  browsers: ['Chrome_no_sandbox'],
    customLaunchers: {
      Chrome_no_sandbox: {
        base: 'Chrome',
        flags: ['--no-sandbox']
      }
    },
    ...
```

## Additional Options ##

If the Angular application consumes an API available from another container, it is possible to link it and, if required, connect the container to the same network. In this case, it is recommended to have an Angular environment that configures where to find the API. The name of the linked container can be used as it resolves to the proper IP address.

```sh
docker run -d -v $(pwd):/workdir --link api-container --network api-container_default --name angular-e2e-container rogargon/angular-e2e
```
The Angular environment for the tests, something like environment-e2e.ts, might be something like:

```javascript
export const environment = {
  production: false,
  api: 'http://api-container:8080'
 };
 ```
