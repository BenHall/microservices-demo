---
layout: default
---

## Weave Demo on Docker (single-host)

The Weave Demo application is packaged using a [Docker Compose](https://docs.docker.com/compose/) file.
This version creates several isolated networks using the weave docker plugin driver.

### Pre-requisites

- Install [Docker](https://www.docker.com/products/overview)
- Install [Weave Scope](https://www.weave.works/install-weave-scope/)
- Install [Weave Net](https://www.weave.works/install-weave-net/)

### Launch Weave Scope or Weave Cloud

Weave Scope (local instance)

    scope launch

Weave Cloud (hosted platform). Get a token by [registering here](http://cloud.weave.works/).

    scope launch --service-token=<token>

### Install & run

<!-- deploy-test-start pre-install -->

    curl -L git.io/weave -o /usr/local/bin/weave
    chmod +x /usr/local/bin/weave

<!-- deploy-test-end -->

### Provision infrastructure

<!-- deploy-test-start create-infrastructure -->

    weave launch
    docker-compose up -d

<!-- deploy-test-end -->

### Run tests

There's a load test provided as a service in this compose file. For more information see [Load Test](#loadtest).  
It will run when the compose is started up, after a delay of 60s. This is a load test provided to simulate user traffic to the application.
This will send some traffic to the application, which will form the connection graph that you view in Scope or Weave Cloud. 

You may also choose to run the following command to check the health of the deployment.

<!-- deploy-test-start run-tests -->

    sleep 60
    STATUSCODE=$(curl --silent --output /dev/stderr --write-out "%{http_code}" http://localhost/health?nodes=user,catalogue,queue-master,cart,shipping,payment,orders)

    if [ $STATUSCODE -ne 200 ]; then
        echo "$(tput setaf 1)DEPLOY FAILED$(tput sgr0)"
        exit 1
    fi

<!-- deploy-test-end -->


### Cleaning up

<!-- deploy-test-start destroy-infrastructure -->

    docker-compose down
    weave stop

<!-- deploy-test-end -->
