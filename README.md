# Simple Loadtesting for a MeX Backend
This is a very simple set of applications designed to demonstrate how to test an API in order to appropriately size the virtual machine hosting the API.

In the real world, you will want to do this using your application instead of the node API server that is installed in this process. You probably want to use your own testing for the client side as well, but if not the `vegeta` program used in this demo may fit the bill.

# Components
There are two main components to this test:
1. An API server.
2. A Load Generator.

Both of these components are provided as dockerfiles so that they can be deployed as containers.

Additionally, the apiserver is provided with a docker-compose file and a helm chart for deployment.

## API Server
This is located under the [`apiserver`](./apiserver/README.md) directory and is based off the [jsonplaceholder](https://github.com/typicode/jsonplaceholder) program.

## Load Generator
This is located under the [`loadgen`](./loadgen/README.md) directory.

# Usage
This document assumes some familiarity with the MobiledgeX platform and how to deploy a docker-compose based application with a manifest. If you have questions see the [MobiledgeX Dev Center](https://developers.mobiledgex.com) for details.

Additional information is provided in the subdirectories.
