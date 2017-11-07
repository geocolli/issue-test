
# Harmony: Running CX on Minikube on your Mac

## Overview

The CX application can be run on top of Minikube on your Mac laptop. The following page describes how to set that up.

## Pre-requisites

1.  Mac OS X development environment (Linux documentation to follow)
2.  [Virtualbox](https://www.virtualbox.org) is installed. Download the .dmg file from [here](https://www.virtualbox.org/wiki/Downloads) and install it on your laptop.

## Installation

### Download installation make file and scripts

<span style="font-size: 14.0px;">Download the</span> [Makefile](attachments/196804768/196706439) <span style="font-size: 14.0px;">attached to this page.</span>

Download install_cx_minikube.sh from [here](https://cto-github.cisco.com/Harmony/cx/blob/master/build/install_cx_minikube.sh) (make sure it is in the same directory as the Makefile above).

### Install kubectl, minikube, helm and cx!

Change into the directory where the Makefile and install_cx_minikube.sh scripts are located and run the following command:

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: bash; gutter: false; theme: Confluence" data-theme="Confluence">$ make install</pre>

You should see something like this:

<div class="codeHeader panelHeader pdl hide-border-bottom" style="border-bottom-width: 1px;">**Install log** <span class="collapse-source expand-control" style="display:none;"><span class="expand-control-icon icon"> </span><span class="expand-control-text">Expand source</span></span><span class="collapse-spinner-wrapper"></span></div>


<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: bash; gutter: true; theme: Confluence; collapse: true" data-theme="Confluence">curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/darwin/amd64/kubectl
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 49.4M  100 49.4M    0     0  2447k      0  0:00:20  0:00:20 --:--:-- 2848k
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.22.2/minikube-darwin-amd64
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 73.7M  100 73.7M    0     0  3194k      0  0:00:23  0:00:23 --:--:-- 3592k
chmod +x minikube
sudo mv minikube /usr/local/bin
curl -LO https://storage.googleapis.com/kubernetes-helm/helm-v2.6.2-darwin-amd64.tar.gz
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 15.4M  100 15.4M    0     0  3401k      0  0:00:04  0:00:04 --:--:-- 3434k
tar xvzf helm-v2.6.2-darwin-amd64.tar.gz --strip=1
x helm
x LICENSE
x README.md
chmod +x helm
sudo mv helm /usr/local/bin
chmod u+x ./install_cx_minikube.sh
./install_cx_minikube.sh
Running CX installation of minikube

Starting tiller pod...
$HELM_HOME has been configured at /Users/dlapsley/.helm.
Warning: Tiller is already installed in the cluster.
(Use --client-only to suppress this message, or --upgrade to upgrade Tiller to the current version.)
Happy Helming!
"harmony-cx-postmerge" has been added to your repositories
"harmony-cx-postmerge" has been removed from your repositories

Using CX version: 0.1.0-alpha.176.gaa6ff47

Waiting for tiller pod to become ready...

Delete existing deployment if exists
release "harmony-kaas" deleted

Installing CX...
NAME:   harmony-kaas
LAST DEPLOYED: Wed Oct  4 21:17:34 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Secret
NAME             TYPE    DATA  AGE
harmony-kaas-cx  Opaque  11    4s

==> v1/Service
NAME                   CLUSTER-IP  EXTERNAL-IP  PORT(S)                        AGE
harmony-kaas-corc      10.0.0.126  <none>       8082/TCP                       4s
harmony-kaas-db        10.0.0.128  <none>       3306/TCP                       4s
harmony-kaas-registry  10.0.0.37   <none>       8091/TCP                       4s
harmony-kaas-sddc      10.0.0.115  <none>       8087/TCP                       4s
harmony-kaas-wfm       10.0.0.154  <nodes>      8085:31997/TCP,8081:31996/TCP  4s

==> v1beta1/Deployment
NAME                   DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
harmony-kaas-corc      1        1        1           0          4s
harmony-kaas-db        1        1        1           1          4s
harmony-kaas-registry  1        1        1           0          4s
harmony-kaas-sddc      1        1        1           0          3s
harmony-kaas-wfm       1        1        1           0          3s

NOTES:
1\. Get the application URL by running these commands:
  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services harmony-kaas-wfm)
  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
  echo https://$NODE_IP:$NODE_PORT/

Waiting for CX services to be running...

</pre>

Once the deploy has completed, you can run some commands to see the deployment:

<div class="codeHeader panelHeader pdl hide-border-bottom" style="border-bottom-width: 1px;">**Check deployment** <span class="collapse-source expand-control" style="display:none;"><span class="expand-control-icon icon"> </span><span class="expand-control-text">Expand source</span></span><span class="collapse-spinner-wrapper"></span></div>

<pre class="syntaxhighlighter-pre" data-syntaxhighlighter-params="brush: bash; gutter: true; theme: Confluence; collapse: true" data-theme="Confluence">$ kubectl get pods
NAME                                     READY     STATUS    RESTARTS   AGE
harmony-kaas-corc-3269512721-1b49v       1/1       Running   1          5m
harmony-kaas-db-3273150168-7qb5d         1/1       Running   0          5m
harmony-kaas-registry-1183727431-4pzc4   1/1       Running   0          5m
harmony-kaas-sddc-2716127545-bg4r2       1/1       Running   1          5m
harmony-kaas-wfm-3397869455-0b1nb        1/1       Running   0          5m
$ kubectl get deployments
NAME                    DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
harmony-kaas-corc       1         1         1            1           6m
harmony-kaas-db         1         1         1            1           6m
harmony-kaas-registry   1         1         1            1           6m
harmony-kaas-sddc       1         1         1            1           6m
harmony-kaas-wfm        1         1         1            1           6m

</pre>

## Other Commands

The makefile comes with other targets. Here are some of them.

| Command | Description |
| --- | --- | --- | 
| make install | Installs kubectl, minikube, helm and the kx (cx) application. |
| make uninstalls | Uninstalls kubectl, minikube and helm |
| make kx | Installs the kx application using install_cx_minikube.sh. |
| make start | Starts minikube |
| make stop | Stops minikube |
| make hello_run | Starts a simple hello application to test minikube is working. |
| make hello_delete | Deletes the test hello application. (Slightly buggy at the moment.) |


## Notes

The install script has a few minor bugs in it currently. A [review](https://review.ci.dfj.io/#/c/11713/) is in process that should fix those issues for fresh qinikube  installs.

The Makefile is still relatively crude. Any suggestions for improvement greatly appreciated!