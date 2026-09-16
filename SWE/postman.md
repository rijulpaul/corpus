# Postman

Postman is a tool for building, testing, and documenting APIs. You can make HTTP requests, inspect responses, automate tests, and collaborate on API workflows.

## Requests
A HTTP call we send to an API.
We can edit the following fields of a request:
- Method: GET, POST, PUT, PATCH, DELETE
- URL: API Endpoint
- Headers: Auth, Content-Type, etc. (Request Headers)
- Body: Data (JSON, XML, FOrm-data, etc) (Request Body)
- Params: Query Parameters

## Collection
A group of requests organized together (like folders).
You can add scripts, variables, and tests per collection.

## Environment
Set of variables for different setups (e.g., Dev, Staging, Prod).
`{{base_url}} = https://api.devserver.com`
Then in request URL we can use `{{base_url}}/get`

## Testing
You can write test scripts using JavaScript in the “Tests” tab.

```js
pm.test("Status is 200", function () {
  pm.response.to.have.status(200);
});
pm.test("Body has id", function () {
  var jsonData = pm.response.json();
  pm.expect(jsonData).to.have.property("id");
});
```

## Automation with Collection Runner
Go to your collection → click “Run.”
You can run multiple requests sequentially.
Import a CSV/JSON file to test with multiple data sets.
Results show pass/fail of tests.

## Authorization
Common types:
- No Auth
- Basic Auth
- Bearer Token
- OAuth 2.0
You can set auth at the collection or request level.

## Mock Servers
Create mock APIs for frontend testing.
Define example responses.
Share endpoint before backend exists.

## Monitors
Schedule automated runs (e.g., every hour).
Check uptime or performance.
Requires a Postman account.

## Documentation
Auto-generate API docs from collections.
Share via public or team workspaces.

## Flows
a visual way to design and automate API workflows inside Postman — think of it as drag-and-drop API logic without writing scripts.
