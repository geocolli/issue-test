# Harmony : Testing Contiv with Python

## Purpose

This document will show you how to setup your environment so you can execute the automated Python scripts against a test bed using nosetests. This document will focus on the tests run by the QA-Jenkins job, Netplugin_End_To_End ([http://10.193.246.64:8080/job/Netplugin-End-To-End/](http://10.193.246.64:8080/job/Netplugin-End-To-End/))

## Requirements

The test scripts are written in Python 2.x (tested with Python 2.7.12) and require a few additional modules to be installed. Pip install is the recommended method for installing these modules.

*  PyYaml – Python’s YAML library.

*  Requests – this is Python’s HTTP library used for communicating with Contiv’s Rest API.

   Be sure you get Requests version 2.7.0 or later. You may need to have pip to force the

   version to 2.7.0\. (2.13.0 is the latest, but Jenkins uses 2.7.0.)

*  Nose – this is the nosetest base package.

   pip install nose

*  Nose-testconfig – this is a plugin for Nose that lets you specify multiple config files with -

   -tc-config. These config files are in YAML and build a global dictionary called config for use by all the test cases. Entries in later YAML files can overwrite earlier values which is useful if you need to override settings.

*  Paramiko – SSH library for Python. Easily allows you to open a persistent SSH connection to a host, and issue commands to it.

## Test Environment

Currently, two environments are supported:

*  AWS – Vxlan only
*  vCenter – Vlan, Vxlan, Layer 3, ACI

Note: accessing hosts on vCenter requires proxy setup for your laptop. See appendix for details. (Alternatively, you can create a VM on vCenter with the proper direct network access.)

## Clone the contiv/testing repository

All test-related materials are in the private Contiv repository, testing.

*  jenkins_end-to-end.pipeline, jenkins_openshift-end-to-end.groovy – these are the actual Jenkins scripts that are responsible for spinning up the VMs for the test bed, installing Docker, Kubernetes (K8s) or Openshift, creating the cluster, installing Contiv, running applicable tests scripts on the test bed and finally, deleting the test bed (if desired.)

*  aci-e2e/ – directory of ACI end-to-end test scripts

*  e2e/ – utility directory for openshift.

*  ose-vxlan/ – directory for test scripts for openshift.

*  tests/ - directory for e2e test scripts for vCenter / AWS + Docker-Swarm, K8s.

*  util/ - directory of utility scripts & modules


## Get a test bed

A test bed usually consists of 3 hosts (VMs) running Centos 7.4, with the desired version(s) of Docker or K8s installed. The hosts are configured into a cluster with one host being the master. Contiv is then installed, preferably using the installer tarball that customers would use.

The hosts are configured to allow password-less ssh using 1 of two ssh keys: AWS: user: centos, key: doug-cisco.pem
vCenter: user: admin, key: test-vm –sshkey_id_dsa

The easiest way to get a test bed is...run the Netplugin-End-To-End job on QA-Jenkins. Make sure to set Cleanup to “No”, so it will leave the hosts behind after running. This also quickly lets you spin up an environment with the desired container orchestrator, and ensures everything is working properly by running the existing tests on it. Each test will tear down any containers and configuration, so the test bed should be in a working, empty state.

## Get (or create) default.yaml

The YAML file, default.yaml, contains a lot of basic information that tell the test cases how to run. This file is created dynamically by Jenkins if you run the Jenkins Netplugin_End_To_End job and is located on the Jenkins Master or AWS node:

*  Jenkins Master: /var/lib/jenkins/jobs/Netplugin_End_To_End/workspace/testing/config/default.yaml

*  Jenkins AWS node:
    /home/jenkins/jenkins- slave/workspace/Netplugin_End_To_End/testing/config/default.yaml

You can create this file as well. Here’s a sample:

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: python; gutter: false; theme: Confluence" data-theme="Confluence">{contivver: 1.1.4,
host: 15.29.36.203,
orchver: v2plugin,
pass: admin,
port: '10000',
user: admin}</pre>


*  contivver – the release version of Contiv.

*  host – the IP address of the master node in the cluster. You can get the IP addresses foryour cluster from the output of the terraform command run by Jenkins. This is printed in the console log, near the end of the stage, BringUp. It is also stored in the file “output” in the terraform directory ending with build number. For instance, if your Jenkins job was build 1532, then the directory terraform1532 contains the output files from terraform for your job. This way, you can go into this directory, issue the command: “terraform destroy –force” to easily destroy your testbed. Unless terraform crashes (known bug.)

*  orchver – this is the version, or type, of orchestrator used. For k8s this is the normal version, like 1.4.7 or 1.6.6\. For docker this is either “legacy” or “v2plugin”, referring to which version of the contiv installer that is used.

*  user / pass – the user name and password for authenticating to Contiv. These are the default values and should not need to be changed.

*  port – the port number of the Contiv API server. This is the default value and should not need to be changed. (However, it is important to note that Contiv’s lab blocks outgoing port 10000\. This is the reason QA Jenkins has a Jenkins node on AWS.)

Be sure to place default.yaml inside contiv/testing/tests/config

## Run the tests

You are now ready to run the test scripts. By default, nosetests will scan your current directory and all subdirectories for python files that have the word “test” in them, looking for functions or methods that contain the word “test” in them. You can specify a single test file, or even a single test case inside a single test file if you don’t wish to run the entire set. (See the documentation for nosetests more details.)

Otherwise, just go into contiv/testing/tests and run the command:

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: python; gutter: false; theme: Confluence" data-theme="Confluence">sh "cd testing/tests; nosetests -s -v --tc-format yaml --tc-file <path_to>/default.yaml --tc-file config/<PLATFORM>.yaml --tc-file config/<ORCHESTRATOR>.yaml -a $<PLATFORM> -a <ORCHESTRATOR> {-a <ORCHESTRATOR>_<DOCKERVERSION>}</pre>


Flags:

*  -s – Flush the buffer. This gives better, more coherent output.

*  -v – Verbose mode – more output.

*  --tc-format – specify what format the config files use (YAML)

*  --tc-file – specify a config file (you can use multiple files)

*  -a – run tests that have the following attribute, or tag. This way you don’t end upexecuting K8s tests on your Docker-Swarm, etc.

Where:

*  <PLATFORM> - this is either AWS or VCenter. Also used to determine which config file is used.
*  <ORCHESTRATOR> - this is either docker-swarm or kubernetes. Also used to determine which config file is used.

*  <DOCKERVERSION> - if you are using Docker, you will want to specify if you are usingthe legacy version of the plugin or the newer v2plugin version of the plugin. This makes a difference in the workflow for configuring networks.

Example. Say I’m running:

*  PLATFORM = vcenter
*  ORCHESTRATOR = docker-swarm


• <span style="margin-left: 0.0px;">DOCKERVERSION = legacy

The command is:

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: python; gutter: false; theme: Confluence" data-theme="Confluence">nosetests -s -v --tc-format yaml --tc-file config/default.yaml --tc-file config/vcenter.yaml --tc-file config/docker_swarm.yaml -a vcenter -a docker_swarm -a docker_swarm_legacy</pre>


## APPENDIX

### Setting up Proxy to access hosts on vCenter

Why it’s needed: We ran out of IP addresses when setting up the vCenter. So Jenkins’ VMs on vCenter use a range behind a proxy server.
How to configure it (Mac):
Under your ~/.ssh directory create a file named config with the following:

<div class="code panel pdl" style="border-width: 1px;">

<div class="codeContent panelContent pdl">

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: python; gutter: false; theme: Confluence" data-theme="Confluence"> Host 15.29.3?.* !15.29.30.* !15.29.31.*
  ProxyCommand nc -x 10.193.231.10:8111 %h %p
  StrictHostKeyChecking no
  UserKnownHostsFile=/dev/null
  User admin
  IdentityFile ~/.ssh/test-vm-sshkey_id_dsa</pre>

</div>

</div>

###Sample output from Nose

Say I’m running:

*  PLATFORM = vcenter

*  ORCHESTRATOR = docker-swarm

*  DOCKERVERSION = legacy

The command:

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: python; gutter: false; theme: Confluence" data-theme="Confluence">nosetests -s -v --tc-format yaml --tc-file config/default.yaml --tc-file config/vcenter.yaml --tc-file config/docker_swarm.yaml -a vcenter -a docker_swarm -a docker_swarm_legacy</pre>

Runs all tests that are labeled as: vcenter, docker_swarm, and docker_swarm_legacy.


First you get a summary of the tests run, and their results:

<div class="codeContent panelContent pdl">

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: python; gutter: false; theme: Confluence" data-theme="Confluence">api_tests.TestClass.test_01_login_page ... ok api_tests.TestClass.test_02_health_check ... ok api_tests.TestClass.test_03_login ... ok api_tests.TestClass.test_04_add_list_inspect_delete_tenant ... ok api_tests.TestClass.test_05_add_list_inspect_delete_network ... ok api_tests.TestClass.test_06_add_list_inspect_delete_policy_workflow ... ok api_tests.TestClass.test_07_add_list_inspect_delete_endpoint_groups ... ok api_tests.TestClass.test_08_add_list_inspect_delete_networkPolicy ... ok docker-container_tests.TestClass.run_container_on_docker_test ... ok
----------------------------------------------------------------------
Ran 9 tests in 41.316s OK</pre>


The rest is the verbose output by the test scripts themselves (print statements, etc.) for  debugging.

</div>


<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: python; gutter: false; theme: Confluence" data-theme="Confluence">setup
Need token
{'sshuser': 'admin', 'subnet': '10.20.30.0/24', 'nwType': '', 'sshkey': 'test-vm-sshkey_id_dsa', 'ipv6Gateway': '', 'cfgdTag': 'JenkNet', 'ipv6Subnet': '', 'nwTag': 'JenkNet', 'pktTag': 0, 'host': '15.29.37.170', 'tenantName': 'Jenkins', 'gateway': '10.20.30.1', 'user': 'admin', 'encap': 'vxlan', 'pass': 'admin', 'contivver': '1.1.4', 'orchver': 'legacy', 'networkName': 'JenkNet', 'port': '10000'}
<Response [200]>
Ding!
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}}
setup
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}}
setup
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}}
setup
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}}
setup
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}} {u'tenantName': u'DougTenant', u'key': u'DougTenant', u'link-sets': {}} setup
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}} {u'tenantName': u'DougConTen1', u'key': u'DougConTen1', u'link-sets': {}} setup
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}} {u'tenantName': u'DougConTen1', u'key': u'DougConTen1', u'link-sets': {}} setup
==========
{u'Oper': {}, u'Config': {u'policyName': u'DougPolicy1', u'tenantName': u'DougConTen1', u'links': {u'Tenant': {u'type': u'tenant', u'key': u'DougConTen1'}}, u'key': u'DougConTen1:DougPolicy1', u'link-sets': {}}} ==========
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}} {u'tenantName': u'DougConTen1', u'key': u'DougConTen1', u'link-sets': {}} setup
Need token
--------
{'sshuser': 'admin', 'subnet': '10.20.30.0/24', 'nwType': '', 'sshkey': 'test-vm-sshkey_id_dsa', 'ipv6Gateway': '', 'cfgdTag': 'JenkNet', 'ipv6Subnet': '', 'nwTag': 'JenkNet', 'pktTag': 0, 'host': '15.29.37.170', 'tenantName': 'Jenkins', 'gateway': '10.20.30.1', 'user': 'admin', 'encap': 'vxlan', 'pass': 'admin', 'contivver': '1.1.4', 'orchver': 'legacy', 'networkName': 'JenkNet', 'port': '10000'}
===== starting JenkCon0
out :
aaecb8505ac9ff9e370ff31885d1ab4be88f254161d3c200b1b89a0101dc6772
----- err :
=====
===== starting JenkCon1
out : b95a24d27a95d774460e88ab0608be768f6d194627b6cbab881feefadfb9fe96
----- err :
=====
===== ping test
out :
1 packets transmitted, 0 packets received, 100% packet loss
----- err :
=====
===== stop ===== JenkCon0 JenkCon0
-----
=====
===== rm ===== JenkCon0 JenkCon0
-----
=====
===== stop ===== JenkCon1 JenkCon1
-----
=====
===== rm ===== JenkCon1 JenkCon1
-----
=====
{u'tenantName': u'default', u'key': u'default', u'link-sets': {}} {u'tenantName': u'Jenkins', u'key': u'Jenkins', u'link-sets': {}}</pre>
