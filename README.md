<div align="center">
  <img height="250px" src="https://github.com/yuvalherziger/tomodo/raw/main/tomodo-nopg.png" alt="tomodo logo"></img>
</div>

# tomodo-github-action

This repo is home to the [tomodo](https://tomodo.dev) GitHub action.  Use is to run a MongoDB
instance in your GitHub actions for automated tests.  `tomodo` supports local Atlas deployments, standalone instance,
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
      uses: yuvalherziger/setup-node@v4
      with:
        node-version: "20"

    - name: Start a MongoDB Atlas container
      id: tomodo
      uses: yuvalherziger/tomodo-github-action@v1
      with:
        type: "atlas"

    - run: npm install

    # Use the tomodo step's output to grab the connection string - and you're done!
    - run: npm test
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
        image-repo: "mongo"    # default: "mongo" for non-atlas deployments, "mongodb/mongodb-atlas-local" for Atlas
        image-tag: "7.0"       # Change for a specific MongoDB version (default: "latest")
        replicas: "3"          # Number of nodes in a replica-set/shard (default: "3")
        shards: "2"            # Number of shards in a sharded cluster (default: "2")
        config-servers: "3"    # Number of config servers in a sharded cluster (default: "1")
        mongos: "1"            # Number of mongos routers in a sharded cluster (default: "1")
        network-name: "my-net" # Name for the instance's docker network (default: "mongo-network")
```
