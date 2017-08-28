luismayta/alpine-python:3.6.1
=============================

|Build Status|

Requirements
------------

None

Dependencies
------------

none

Usage
-----

In order to run a basic container start a container as follows:

``docker run -P --name alpine-python -e ENV=DEV luismayta/alpine-python:3.6.1``

Environment Variables
---------------------

This is a list of the available environment variables which can be set
at runtime using -e KEY=value. For example, to change the default
environment you can issue
``docker run -P --name alpine-python -e ENV=dev luismayta/alpine-python:3.6.1``

.. CI Github
.. |Build Status| image:: https://travis-ci.org/luismayta/docker-alpine-python.svg
   :target: https://travis-ci.org/luismayta/docker-alpine-python
