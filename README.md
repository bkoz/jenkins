# OpenShift jenkins demo
Taken from https://github.com/openshift/origin/tree/master/examples/jenkins
Adapted for OpenShift Enterprise v3.1
```
$ oc new-project test
$ oc policy add-role-to-user edit system:serviceaccount:test:default
$ oc new-app --template=jenkins-ephemeral
$ oc new-app application-template-koz.json --param=GIT_URI=https://github.com/bkoz/nodejs-ex.git
$ oc expose service frontend-prod
$ oc get routes
```
Watch pod activity.
```
$ oc get pods -o wide -w
```
Examine the image streams before and after the Jenkins build and take note of the TAG column.
Visit the Jenkins site and start the build.
```
$ oc get is
```
