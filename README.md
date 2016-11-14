# OpenShift/Jenkins demo notes
Taken from https://github.com/openshift/origin/tree/master/examples/jenkins

Adapted for OpenShift Enterprise v3.2 and will probably not work with v3.3 until I figure out jenkins->openshift authentication.
```
$ git clone https://github.com/bkoz/jenkins.git
$ cd jenkins
$ oc new-project test
$ oc policy add-role-to-user edit system:serviceaccount:test:default
$ oc new-app --template=jenkins-ephemeral --param=JENKINS_PASSWORD=jenkins
$ oc new-app application-template-koz.json --param=GIT_URI=https://github.com/bkoz/nodejs-ex.git
$ oc expose service frontend-prod
$ oc get routes
```
Watch for the jenkins pod to become ready and visit it's route. 
```
$ oc get pods
```
Login to Jenkins and create the following pipeline job.

DevProd pipeline
```
node {
       stage 'buildInDev'
       openshiftBuild(buildConfig: 'frontend', showBuildLogs: 'true')
       stage 'deployInDev'
       openshiftDeploy(deploymentConfig: 'frontend')
       stage "deployToProd"
       input message: 'Promote to production ?', ok: '\'Yes\''
       echo "Deploying to server."
       openshiftTag(sourceStream: 'origin-nodejs-sample', sourceTag: 'latest', destinationStream: 'origin-nodejs-sample', destinationTag: 'prod')
       openshiftScale(deploymentConfig: 'frontend-prod',replicaCount: '1')
}
```
Examine the image streams before and after the Jenkins build and take note of the TAG column.
Visit the Jenkins site and start the dev pipeline.

Use the OpenShift CLI to delete the prod tag:
```
oc tag origin-nodejs-sample:prod --delete=true
```

References

https://blog.openshift.com/openshift-3-3-pipelines-deep-dive/

https://github.com/jenkinsci/openshift-pipeline-plugin#jenkins-pipeline-formerly-workflow-plugin


