# OpenShift/Jenkins demo notes
Taken from https://github.com/openshift/origin/tree/master/examples/jenkins

The basic Jenkins pipeline demo adapted for OpenShift Enterprise v3.2.
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
       openshiftVerifyDeployment(deploymentConfig: 'frontend')
       stage "deployToProd"
       input message: 'Promote to production ?', ok: '\'Yes\''
       echo "Deploying to production."
       openshiftTag(sourceStream: 'origin-nodejs-sample', sourceTag: 'latest', destinationStream: 'origin-nodejs-sample', destinationTag: 'prod')
       openshiftScale(deploymentConfig: 'frontend-prod',replicaCount: '1')
}
```
Login to the Jenkins console and start the pipeline build. Login to OpenShift to watch the build and deployments. Examine the image streams before and after the Jenkins build and take note of the TAG column.

If you need to delete the image stream tag, use the OpenShift CLI.
```
oc tag origin-nodejs-sample:prod --delete=true
```

References

https://blog.openshift.com/openshift-3-3-pipelines-deep-dive/

https://github.com/jenkinsci/openshift-pipeline-plugin#jenkins-pipeline-formerly-workflow-plugin


