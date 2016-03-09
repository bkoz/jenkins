# OpenShift jenkins demo
Taken from https://github.com/openshift/origin/examples/jenkins
Adapted for OpenShift Enterprise v3.1
```
$ oc new-app --template=jenkins-ephemeral
$ oc new-app application-template-koz.json
$ oc expose service frontend-prod
$ oc get routes
```
Visit the Jenkins route and start the build.
Examine the image streams before and after the Jenkins build and take note of the TAG column.
```
$ oc get is
```
