# Harmony : Contiv TOI


This document is designed to help newcomers to Contiv get ramped up more quickly.

# The purpose of each repo under the Contiv project

## Important/Major repos

Auth Proxy
- URL: [https://github.com/contiv/auth_proxy](https://github.com/contiv/auth_proxy)
- Purpose: auth_proxy is a proxy providing TLS termination and authentication + authorization (RBAC) before forwarding requests to an upstream `netmaster`. Authentication is done with LDAP/AD or local users. It exposes the same routes as `netmaster` and can be accessed using `netctl`. It also hosts the Contiv UI (see contiv-ui repo).

Contiv UI
- URL: [https://github.com/contiv/contiv-ui](https://github.com/contiv/contiv-ui)
- Purpose: Contiv UI provides a GUI that allows administrators and regular users to perform many of the operations that can be performed by the `netctl` command line tool.

[contiv.github.io](http://contiv.github.io)
- URL: [https://github.com/contiv/contiv.github.io](https://github.com/contiv/contiv.github.io)
- Purpose: this repo contains the source code generating [https://contiv.github.io/](https://contiv.github.io/). See [CONTRIBUTING.md](http://CONTRIBUTING.md) for more details on how to use this repo.

Install
- URL: [https://github.com/contiv/install](https://github.com/contiv/install)
- Purpose: Contiv installer and demo code (see Makefile's `demo-*` targets)

Netplugin
- URL: [https://github.com/contiv/netplugin](https://github.com/contiv/netplugin)
- Purpose: the actual networking plugin for Docker/Kubernetes/Nomad, etc.

Testing
- URL: [https://github.com/contiv/testing](https://github.com/contiv/testing)
- Purpose: this repo contains the end-to-end pipeline script and various other testing files
- testing/jenkins_end-to-end.pipeline - this is the Jenkins Pipeline script used by the Jenkins job, Netplugin-End-To-End. This script expects a set of parameters from Jenkins that specify which CONTIV_VERSION, PLATFORM, ORCHESTRATOR, and VERSION are to be used. Each permutation of Platform, Orchestrator and Version requires a slightly different workflow to bring up an environment of 3 nodes with Contiv installed.
- testing/util - contains utility scripts used both by the Pipeline script as well as the test cases
- testing/tests - this contains the various test sets and supporting files.
- testing/tests/config - this contains the configuration YAML files. These are specific to the Jenkins test environment. If you wish to run tests locally you should create your own local versions.
- testing/tests/data - this contains any data files used by the tests. Currently just a pair of YAML files specifying a K8s Pod with a single container running busybox.

The test case scripts are written with the Nosetests framework in mind, and use its plugin of tc-configfile. By default, Nosetests will search the current directory for test cases - this means any file, module, and function that has the word "test" (or any combination of upper & lower case) in it. The tests have tags applied which match the parameters from Jenkins. This way only the test cases for the applicable Platform, Orchestrator and Version are executed. This also makes it easy for someone to add and extend test cases without having to modify the Pipeline script at all.

## Supporting repos

aci-gw
- URL: [https://github.com/contiv/aci-gw](https://github.com/contiv/aci-gw)
- Purpose: this repo implements a REST gateway which allows `netmaster` to program ACI

Ansible
- URL: [https://github.com/contiv/ansible](https://github.com/contiv/ansible)
- Purpose: this repo holds ansible playbooks used by other repos under Contiv.

build
- URL: [https://github.com/contiv/build](https://github.com/contiv/build)
- Purpose: Packer configuration for building Vagrant boxes used by our Vagrantfiles

contivmodel
- URL: [https://github.com/contiv/contivmodel](https://github.com/contiv/contivmodel)
- Purpose: contains Contiv's object model which is used by the netplugin repo. Parts of this repo are code-generated from the modelgen repo

libopenflow
- URL: [https://github.com/contiv/libOpenflow](https://github.com/contiv/libOpenflow)
- Purpose: Openflow 1.3 protocol library

modelgen
- URL: [https://github.com/contiv/modelgen](https://github.com/contiv/modelgen)
- Purpose: templates and configuration for model/code generation

objdb
- URL: [https://github.com/contiv/objdb](https://github.com/contiv/objdb)
- Purpose: object storage and retrieval library used by netplugin

ofnet
- URL: [https://github.com/contiv/ofnet](https://github.com/contiv/ofnet)
- Purpose: this repo allows you to manage OVS installations

remotessh
- URL: [https://github.com/contiv/remotessh](https://github.com/contiv/remotessh)
- Purpose: library which allows Go code to SSH into and run commands on other machines. Used by netplugin test code.

stats_exporter
- URL: [https://github.com/contiv/stats_exporter](https://github.com/contiv/stats_exporter)
- Purpose: stats_exporter is the code behind a Docker image which acts as a sidecar pod and runs alongside `netmaster` and `netplugin`. It exposes OVS metrics and other data over HTTP for consumption by Prometheus. This was a 2017 summer intern project.

VPP
- URL: [https://github.com/contiv/vpp](https://github.com/contiv/vpp)
- Purpose: used by the team ([https://github.com/orgs/contiv/teams/vpp-friends/members](https://github.com/orgs/contiv/teams/vpp-friends/members)) working on VPP<->Contiv integration

## Other repos

ccn

* URL: [https://github.com/contiv/ccn](https://github.com/contiv/ccn)
* Purpose: this is the main place where we tracked issues: [https://github.com/contiv/ccn/issues](https://github.com/contiv/ccn/issues)

contiv-mgmt

* URL: [https://github.com/contiv/contiv-mgmt](https://github.com/contiv/contiv-mgmt)
* Purpose: this repo appears to be issues created by Meenakshi for product management. This stuff could probably be migrated elsewhere.

deploy

* URL: [https://github.com/contiv/deploy](https://github.com/contiv/deploy)
* Purpose: ancient Contiv deployment code, not touched in over 1.5 years. Can probably be deleted now.

errored

* URL: [https://github.com/contiv/errored](https://github.com/contiv/errored)
* Purpose: library which extends Go's error type

executor

* URL: [https://github.com/contiv/executor](https://github.com/contiv/executor)
* Purpose: library which extends exec.Cmd, used by testing code

middleman-hashicorp

* URL: [https://github.com/contiv/middleman-hashicorp](https://github.com/contiv/middleman-hashicorp)
* Purpose: fork of an old version of some hashicorp extensions to `middleman`... needed to build [contiv.github.io](http://contiv.github.io)

product-mgmt

* URL: [https://github.com/contiv/product-mgmt](https://github.com/contiv/product-mgmt)
* Purpose: looks like Balaji (old marketing/product guy for contiv) was keeping track of stuff here... might be worth looking over the Issue list and seeing if anything should be ported to Jira.

Slack

* URL: [https://github.com/contiv/slack](https://github.com/contiv/slack)
* Purpose: implements the Contiv Slack self-invite site: [https://contiv.herokuapp.com/](https://contiv.herokuapp.com/)

Stash

* URL: [https://github.com/contiv/stash](https://github.com/contiv/stash)
* Purpose: random place to store things... has content going back to 2014\. This also contains our documents which show Lab Server allocation and such.

# Installation workflow

[https://github.com/contiv/install/blob/master/README.md](https://github.com/contiv/install/blob/master/README.md)

# Development workflow

[https://github.com/contiv/netplugin/blob/master/docs/DevEnv.md](https://github.com/contiv/netplugin/blob/master/docs/DevEnv.md)

# High level coding structure

Design [https://github.com/contiv/netplugin/blob/master/docs/Design.md](https://github.com/contiv/netplugin/blob/master/docs/Design.md)

# Release workflow

See the Contiv Release Guide: [https://github.com/contiv/ccn/wiki/Contiv-Release-Guide](https://github.com/contiv/ccn/wiki/Contiv-Release-Guide)

# CI

Contiv CI - [http://10.193.246.160/](http://10.193.246.160/) - CI jobs for open source projects on Github

Contiv QA - [http://10.193.246.64:8080/](http://10.193.246.64:8080/) - Internal jobs, e2e testing, etc.

Important Jobs:

* Netplugin-End-To-End : The workhorse job. Will spin up 3 VMs on the specified platform, install Docker or Kubernetes on the hosts, then Contiv. It then runs the applicable test cases (see Testing, above). Finally, if everything succeeds, the test environment is torn down.

* Netplugin_end_to_end_test_master : This is the "nightly test" job. It starts up around 8-10pm every night, against the "latest" version of Contiv, and runs tests on the following matrix:
* AWS, K8s 1.6.4
* vCenter, Docker Swarm "Legacy mode"
* vCenter, Docker Swarm "V2plugin"
* vCenter, K8s, 1.4.7
* vCenter, k8s, 1.6.4

Test results are published on Jenkins, as well as on the old Contiv Slack under the Jenkins channel.

* Netplugin_e2e_banger: This is essentially the same as Netplugin_end_to_end_test_master stuck inside a FOR loop. Easily allows you to repeat a set of End-to-End jobs. Useful for helping debug race conditions or other transient problems.

# Customer use cases

_will be filled in after sprint planning meeting_