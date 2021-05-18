# Load Testing
This directory contains a very simple load test that can be deployed as a docker container. The `dockerfile` shows the requirements, but at a high level you require the [vegeta](https://github.com/tsenart/vegeta) executable (the provided version is for amd64) and the `jq` json utility.

## Full Requirements
- `jq` to handle the processing and creation of the json payload.
- `vegeta` to run the tests.
- `env` if you choose to not use the hardcoded values of 30 seconds at 30 connections/second or if you choose not to pass the variables in the environment.
- `run.sh` to serve as the entrypoint for the container.

## How to Run

### Build the docker image
```
docker build -t loadtest .
Sending build context to Docker daemon  8.694MB
Step 1/8 : FROM ubuntu:latest
 ---> 1d622ef86b13
Step 2/8 : RUN apt update
 ---> Using cache
 ---> 159214b0992d
Step 3/8 : RUN apt -y install jq ca-certificates
 ---> Using cache
 ---> bed64e1fc256
Step 4/8 : COPY vegeta /usr/local/bin/vegeta
 ---> Using cache
 ---> 16c04779928a
Step 5/8 : COPY run.sh /usr/local/bin/run.sh
 ---> Using cache
 ---> d297b14ff017
Step 6/8 : ENV THERATE=30
 ---> Using cache
 ---> cf88aa198229
Step 7/8 : ENV THEDURATION=30s
 ---> Using cache
 ---> d0bad762f288
Step 8/8 : ENTRYPOINT ["/usr/local/bin/run.sh"]
 ---> Using cache
 ---> c0a310d63490
Successfully built c0a310d63490
Successfully tagged loadtest:latest
```

### Run the docker image

```
docker run --rm --env-file ./env loadtest:latest
POST TEST: Rate is 50/s DURATION is 60s
Requests      [total, rate, throughput]         3000, 50.02, 30.89
Duration      [total, attack, wait]             1m24s, 59.98s, 24.425s
Latencies     [min, mean, 50, 90, 95, 99, max]  2.835ms, 12.195s, 10.734s, 27.025s, 30.002s, 30.003s, 30.023s
Bytes In      [total, mean]                     44319, 14.77
Bytes Out     [total, mean]                     27570, 9.19
Success       [ratio]                           86.90%
Status Codes  [code:count]                      0:393  201:2607
Error Set:
Post "https://hamburg-main.tdg.mobiledgex.net:10001/posts": EOF
Post "https://hamburg-main.tdg.mobiledgex.net:10001/posts": net/http: request canceled (Client.Timeout exceeded while awaiting headers)
Post "https://hamburg-main.tdg.mobiledgex.net:10001/posts": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
Post "https://hamburg-main.tdg.mobiledgex.net:10001/posts": context deadline exceeded
GET TEST: Rate is 50/s DURATION is 60s
Requests      [total, rate, throughput]         3000, 50.02, 49.86
Duration      [total, attack, wait]             1m0s, 59.98s, 170.875ms
Latencies     [min, mean, 50, 90, 95, 99, max]  167.057ms, 176.698ms, 169.769ms, 171.22ms, 172.171ms, 491.49ms, 1.029s
Bytes In      [total, mean]                     71605, 23.87
Bytes Out     [total, mean]                     0, 0.00
Success       [ratio]                           99.97%
Status Codes  [code:count]                      200:2999  404:1
Error Set:
404 Not Found
```

## Things to Change
- The `env` file contains three variables, `THEURL` should be set to the URL of the api server being used for the test, including the port. Two additional variables, `THERATE` and `THEDURATION` can be used to adjust the number of api requests made per second and how long the test will last.
- In addition to using the `env` file it is possible to pass variables into the load generator using `-e` or `--env`.
- The `run.sh` script is very simple; feel free to adjust that to use some of the more advanced features of `vegeta`. - As it exists, the `run.sh` script tests using POSTs and GETs; the script can be modified and extended as required in order to perform the tests needed for your use case.
- If you run from different locations (ie, a multi-client load test) you may want to adjust the index that is used in the `run.sh` script a bit to prevent key collisions.

## Run in MobidledgeX

1. Build tag and push the image: 

```
IMAGE=myimagename
VERSION=1.0
ORG=myMexOrg
docker image build -t ${IMAGE}:${VERSION} .
docker login -u {username} docker.mobiledgex.net
docker tag pgloadtest:1.4 docker.mobiledgex.net/${ORG}/images/${IMAGE}:${VERSION}
docker push docker.mobiledgex.net/${ORG}/images/${IMAGE}:${VERSION}
```

2. Create an Docker App in MeX. Dont use Image Path, Instead use the `docker-compose.yml` file contents in the "Deployment Manifest", adjusted to match your iamge name and the URI of your api server.

3. Create an Instance for the App.

You can now easily adjust the `THERATE`, `THEDURATION` and even `THEURL` simple by editing the App and then upgarding the instance via the App Instances tab.
