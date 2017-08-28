luismayta/alpine-python:latest
==============================

|Build Status| |GitHub issues| |GitHub license|

Requirements
------------

None

Dependencies
------------

none

Usage
-----

In order to run a basic container start a container as follows:

``docker run -P --name alpine-python -e ENV=DEV luismayta/alpine-python:latest``

Environment Variables
---------------------

This is a list of the available environment variables which can be set
at runtime using -e KEY=value. For example, to change the default
environment you can issue
``docker run -P --name alpine-python -e ENV=dev luismayta/alpine-python:latest``

.. |Build Status| image:: https://travis-ci.org/luismayta/docker-alpine-python.svg
   :target: https://travis-ci.org/luismayta/docker-alpine-python
.. |GitHub issues| image:: https://img.shields.io/github/issues/luismayta/docker-alpine-python.svg
   :target: https://github.com/luismayta/docker-alpine-python/issues
.. |GitHub license| image:: https://img.shields.io/github/license/mashape/apistatus.svg?style=flat-square
   :target: LICENSE
