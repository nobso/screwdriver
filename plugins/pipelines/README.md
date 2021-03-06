# Pipelines Plugin
> Hapi pipelines plugin for the Screwdriver API

## Usage

### Register plugin

```javascript
const Hapi = require('hapi');
const server = new Hapi.Server();
const pipelinesPlugin = require('./');

server.connection({ port: 3000 });

server.register({
    register: pipelinesPlugin,
    options: {}
}, () => {
    server.start((err) => {
        if (err) {
            throw err;
        }
        console.log('Server running at:', server.info.uri);
    });
});
```

### Routes

#### Get all pipelines
`page`, `count`, `sort`, `sortBy`, `search`, and `configPipelineId` optional
`search` will search for a pipeline with a name containing the search keyword in the `scmRepo` field

`GET /pipelines?page={pageNumber}&count={countNumber}&configPipelineId={configPipelineId}&search={search}`

#### Get single pipeline

`GET /pipelines/{id}`

#### Create a pipeline
Create a pipeline and create a job called 'main'

`POST /pipelines`

**Arguments**

* `checkoutUrl` - Source code URL for the application. For a git-based repository, it is typically the SSH endpoint and the branch name, separated by a octothorpe. Must be unique.
* `configUrl` - *Optional* Source code URL for Screwdriver configuration, if it is in a different location than the
source code. For a git-based repository, it is typically the SSH endpoint and the branch name, separated by a octothorpe.

Example payload:
```json
{
  "checkoutUrl": "git@github.com:screwdriver-cd/data-model.git#master",
  "configUrl": "git@github.com:screwdriver-cd/optional-config.git#master"
}
```

#### Update a pipeline
You can update the checkoutUrl of a pipeline.

`PUT /pipelines/{id}`

**Arguments**

* `checkoutUrl` - Source code URL for the application. For a git-based repository, it is typically the SSH endpoint and the branch name, separated by a octothorpe. Must be unique.

Example payload:
```json
{
  "checkoutUrl": "git@github.com:screwdriver-cd/data-model.git#master"
}
```

#### Delete a pipeline

`DELETE /pipelines/{id}`

#### Synchronize a pipeline
* Synchronize the pipeline by looking up latest screwdriver.yaml
* Create, update, or disable jobs if necessary.
* Store/update the pipeline workflowGraph

`POST /pipelines/{id}/sync`

#### Synchronize webhooks
* Synchronize webhooks for the pipeline
* Add or update webhooks if necessary

`POST /pipelines/{id}/sync/webhooks`

#### Synchronize pull requests
* Synchronize pull requests for the pipeline
* Add or update pull request jobs if necessary

`POST /pipelines/{id}/sync/pullrequests`

#### Get all pipeline events
`page`, `count`, and `sort` are optional

`GET /pipelines/{id}/events?page={pageNumber}&count={countNumber}&sort={sort}`

#### Get all jobs (including pull requests jobs)
`archived` is optional and has a default value of `false`, which makes the endpoint not return archived jobs (e.g. closed pull requests)

`GET /pipelines/{id}/jobs?archived={boolean}`

#### Get all pipeline secrets

`GET /pipelines/{id}/secrets`

#### Start all child pipelines belong to this pipeline
* Start all child pipelines belong to this config pipeline all at once

`POST /pipelines/{id}/startall`

#### Create a pipeline token

`POST /pipelines/{id}/token`

#### Get all pipeline tokens

`GET /pipelines/{id}/tokens`

#### Update a pipeline token

`PUT /pipelines/{pipelineId}/tokens/{tokenId}`

#### Refresh a pipeline token

`PUT /pipelines/{pipelineId}/tokens/{tokenId}/refresh`

#### Delete a pipeline token

`DELETE /pipelines/{pipelineId}/tokens/{tokenId}`

#### Delete all pipeline tokens belong to this pipeline

`DELETE /pipelines/{pipelineId}/tokens`

### Access to Factory methods
The server supplies factories to plugins in the form of server settings:

```js
// handler pipelinePlugin.js
handler: (request, reply) => {
    const factory = request.server.app.pipelineFactory;

    // ...
}
```
