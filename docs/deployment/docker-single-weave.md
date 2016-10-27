---
layout: default
---

## Weave Demo on Docker (single-host)

The Weave Demo application is packaged using a [Docker Compose](https://docs.docker.com/compose/) file.
This version creates several isolated networks using the weave docker plugin driver.

### Pre-requisites

- Install [Docker](https://www.docker.com/products/overview)
- Install [Weave Net](https://www.weave.works/install-weave-net/)

### Install & run

<!-- deploy-test-start pre-install -->

    apt-get install -yq curl

    curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose

    curl -L git.io/weave -o /usr/local/bin/weave
    chmod +x /usr/local/bin/weave

<!-- deploy-test-end -->

### Provision infrastructure

<!-- deploy-test-start create-infrastructure -->

    weave launch
    docker-compose up -d

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
    weave stop

<!-- deploy-test-end -->
