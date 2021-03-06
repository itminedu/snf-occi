Build the image
---------------

::

  $ docker build -t snf-occi-client:<version> .

This will build a new image, which can be pushed or kept local.

Alternatively, you can use the grnet repository to build the image:

::

  $ docker build -t snf-occi-client:<version> https://github.com/grnet/snf-occi.git#develop:soi/tests/functional

Run a new container
-------------------
You need either a client proxy or a pair of keys.

If you have a pair of keys, load the directory containing them as /root/.globus
Then run the appropriate commands to generate a proxy

::

  $ docker run -v ${HOME}/.globus:/root/.globus -ti snf-occi-client:latest
  # fetch-crl -p 20
  # voms-proxy-init -voms fedcloud.egi.eu -rfc

If you already have a valid proxy, load it somewhere on the machine

::

  $ docker run -v my_proxy:/data/my_proxy  -ti snf-occi-client:latest

What is in the container
------------------------
You will find all the tools from egifedcloud/fedcloud-userinterface:latest plus
two shell script to test the snf-occi application.

To run the tests in debug mode (verbose) set the environment variable

export SNF_OCCI_DEBUG=true

Unset the variable for a less verbose execution

There are two equivalent sets of functional tests prepared in two separate
directories. The ones based on the rOCCI client are under `rocci` directory,
the ones based on curl are under the `curl` directory. In the following we will
assume you want to run the tests for "compute" (server) functionalities, but
you should explore and run all tests.

To run the "rocci/compute.sh" script, you must set some variables,
either when you RUN the container or while you are inside the container.

::

  OCCI_ENDPOINT
  USER_PROXY or TOKEN
  OS_TPL
  RESOURCE_TPL

The "rocci/storage.sh" script can run with only the first two variables set.

For the "curl/compute.sh" you need a similar set of enviroment variables.

::

  OCCI_ENDPOINT
  TOKEN
  OS_TPL
  RESOURCE_TPL

The "curl/storage.sh" script can run with only the first two variables set.

For instance, if you have a proxy, you may want to run something like:

::

  $ docker run --name occi_client -v my_proxy:/tmp/x509up_u0 \
    -e OCCI_ENDPOINT="https://okeanos-occi2.hellasgrid.gr:9000" \
    -e TOKEN="My-Okeanos-Token" \
    -e USER_PROXY="/tmp/x509up_u0" \
    -e OS_TPL="6f1f7205-cf4c-4b8c-ae77-7c419747bcbd"\
    -e RESOURCE_TPL="13"\
    -ti snf-occi-client:latest

TIP: To test on the same host (e.g. with a server running localy), run the
command with the "--net host" argument

