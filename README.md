<div align="center">
  <img height="250px" src="https://github.com/yuvalherziger/tomodo/raw/main/tomodo-nopg.png" alt="tomodo logo"></img>
</div>

# tomodo-github-action

This repo is home to the [tomodo](https://tomodo.dev) GitHub action.  Use is to run a MongoDB
instance in your GitHub actions for automated tests.  `tomodo` supports local Atlas deployments, standalone instances,
replica sets, and sharded clusters.

## Usage

Below is a sample GitHub action that uses

```yaml
name: Run unit test
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - name: Git checkout
      uses: actions/checkout@v4

    - name: Set up Node.js
      uses: actions/setup-node@v4
      with:
        node-version: "20"

    - name: Start a MongoDB Atlas container
      id: tomodo
      uses: yuvalherziger/tomodo-github-action@v1
      with:
        instance-type: "atlas"

    - run: npm install

    # Use the tomodo step's output to grab the connection string - and you're done!
    - name: Run tests
      run: npm test
      env:
        MONGODB_URI: ${{ steps.tomodo.outputs.connection-string }}
```

## Configuration

```yaml
name: Run unit test

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Git checkout
      uses: actions/checkout@v4

    - name: Start a MongoDB Atlas container
      id: tomodo
      uses: yuvalherziger/tomodo-github-action@v1
      with:
        instance-type: "atlas" # "atlas", "standalone", "replica-set", "sharded" (default: "atlas")
        username: "user"       # Omit to skip authentication (default: "")
        password: "pass"       # Omit to skip authentication (default: "")
        port: "27017"          # Change to a non-default port (default: "27017")
        image-tag: "7.0"       # Change for a specific MongoDB version (default: "latest")
        replicas: "3"          # Number of nodes in a replica-set/shard (default: "3")
        shards: "2"            # Number of shards in a sharded cluster (default: "2")
        config-servers: "3"    # Number of config servers in a sharded cluster (default: "1")
        mongos: "1"            # Number of mongos routers in a sharded cluster (default: "1")
        network-name: "my-net" # Name for the instance's docker network (default: "mongo-network")
```

## Input Parameters

The action accepts the following parameters:

| Name             | Description                                                      | Type                                                    | Default         |
|------------------|------------------------------------------------------------------|---------------------------------------------------------|-----------------|
| `name`           | The deployment's name                                            | String                                                  | auto-generated  |
| `instance-type`  | The MongoDB instance type                                        | One of: `atlas`, `standalone`, `replica-set`, `sharded` | `atlas`         |
| `username`       | Optional authentication username                                 | String                                                  | -               |
| `password`       | Optional authentication password                                 | String                                                  | -               |
| `port`           | MongoDB port (in a cluster, this is the first incrementing port) | Integer; Min: `0`, Max: `65535`                         | `27017`         |
| `image-tag`      | The MongoDB image tag (determines the MongoDB version)           | String                                                  | `latest`        |
| `replicas`       | The number of replica set nodes to provision                     | One of: `1`, `3`, `5` ,`7`                              | `3`             |
| `shards`         | The number of shards to provision (sharded cluster)              | Integer (positive)                                      | `2`             |
| `config-servers` | The number of config server replica set nodes (sharded cluster)  | One of: `1`, `3`, `5` ,`7`                              | `1`             |
| `mongos`         | The number of mongos routers (sharded cluster)                   | Integer (positive)                                      | `1`             |
| `network-name`   | The Docker network to provision the deployment in                | string                                                  | `mongo-network` |
