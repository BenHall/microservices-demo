---
layout: default
---

## Sock Shop on Docker

The Sock Shop application is packaged using a [Docker Compose](https://docs.docker.com/compose/) file.
*This version does not use any custom networks*.
DNS is achieved by using the internal Docker DNS, which reads network alias entries provided by docker-compose.

### Pre-requisites

- Install Docker
- Install [Weave Scope](https://www.weave.works/install-weave-scope/)

<!-- deploy-test-start pre-install -->

    apt-get install -yq curl

    curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose

<!-- deploy-test-end -->


### Provision infrastructure

<!-- deploy-test-start create-infrastructure -->

    docker-compose up -d user-db user catalogue-db catalogue rabbitmq queue-master cart-db cart orders-db shipping payment orders front-end edge-router

<!-- deploy-test-end -->

### Run tests

Run the user similator load test. For more information see [Load Test](#loadtest)
This is a load test provided to simulate user traffic to the application.
This will send some traffic to the application, which will form the connection graph that you view in Scope or Weave Cloud.

Also run a test to determine the health of the deployment.

<!-- deploy-test-start run-tests -->

    docker run weaveworksdemos/load-test -d 60 -h edge-router -c 3 -r 10

    STATUSCODE=$(curl --silent --output /dev/stderr --write-out "%{http_code}" http://localhost/health)
    if [ $STATUSCODE -ne 200 ]; then
        echo "$(tput setaf 1)DEPLOY FAILED$(tput sgr0)"
        exit 1
    fi

<!-- deploy-test-end -->

### Cleaning up

<!-- deploy-test-start destroy-infrastructure -->

    docker-compose stop
    docker-compose rm -f

<!-- deploy-test-end -->

### Launch Weave Scope or Weave Cloud

Weave Scope (local instance)

    scope launch

Weave Cloud (hosted platform). Get a token by [registering here](http://cloud.weave.works/).

    scope launch --service-token=<token>

### Load test

There's a load test provided as a service in this compose file.
It will run when the compose is started up, after a delay of 60s.
