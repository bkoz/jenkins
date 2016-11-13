# OpenShift jenkins demo
Taken from https://github.com/openshift/origin/tree/master/examples/jenkins
Adapted for OpenShift Enterprise v3.1
```
$ git clone https://github.com/bkoz/jenkins.git
$ cd jenkins
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
Create 2 pipelines in Jenkins. One to build the dev image and a second to
tag (promote) the image to production. Perhaps the 2 pipelines could be stages in a single pipeline with
an approval process between the stages?

Dev

```
node {
       stage 'build'
       openshiftBuild(buildConfig: 'frontend', showBuildLogs: 'true')
       stage 'deploy'
       openshiftDeploy(deploymentConfig: 'frontend')
}
```
Promote pipeline
```
node {
       stage 'tag'
       openshiftTag(sourceStream: 'origin-nodejs-sample', sourceTag: 'latest', destinationStream: 'origin-nodejs-sample', destinationTag: 'prod')
}
```
$ oc get is
```
Delete the prod tag:
```
oc tag origin-nodejs-sample:prod --delete=true
```
To manually tag the image stream:
```
oc tag origin-nodejs-sample:latest origin-nodejs-sample:prod
```

References:
https://blog.openshift.com/openshift-3-3-pipelines-deep-dive/
https://github.com/jenkinsci/openshift-pipeline-plugin#jenkins-pipeline-formerly-workflow-plugin


