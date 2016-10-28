---
layout: default
---

## Docker Swarm

Please refer to the [new Docker Swarm introduction](http://container-solutions.com/hail-new-docker-swarm/)

### Blockers

Currently, new Docker Swarm does not support running containers in privileged mode.
Maybe it will be allowed in the future.
Please refer to the issue [1030](https://github.com/docker/swarmkit/issues/1030#issuecomment-232299819).
This prevents running Weave Scope in a normal way, since it needs privileged mode.
A work around exists documented [here](https://github.com/weaveworks/scope-global-swarm-service)

Running global plugins is not supported either.

# Pre-requisities

* [Vagrant](https://www.vagrantup.com/downloads.html)
* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

or

* Docker For Mac (limited to a single node)


# How-to using Docker For Mac

* Put your docker into the swarm mode
* Execute the services startup script
* Navigate to http://localhost:30000 to verify that the demo works.

<!-- deploy-test-start pre-install -->

    docker swarm init 2>/dev/null
    sh ./start-swarmkit-services.sh

<!-- deploy-test-end -->

### Run tests

Run the load test provided to simulate user traffic to the application. For more information see [Load Test](#loadtest).  
This will send some traffic to the application, which will form the connection graph that you view in Scope or Weave Cloud. 

You may also choose to run the following command to check the health of the deployment.

<!-- deploy-test-start run-tests -->

    docker run --rm weaveworksdemos/load-test -d 60 -h http://localhost:30000 -c 3 -r 10

    STATUSCODE=$(curl --silent --output /dev/stderr --write-out "%{http_code}" http://localhost:30000/health?nodes=user,catalogue,queue-master,cart,shipping,payment,orders)

    if [ $STATUSCODE -ne 200 ]; then
        echo "\033[31mDEPLOY FAILED\033[0m"
        exit 1
    fi

<!-- deploy-test-end -->

### Cleaning up

<!-- deploy-test-start destroy-infrastructure -->

    ./start-swarmkit-services.sh cleanup

<!-- deploy-test-end -->
