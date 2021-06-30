properties([
     parameters([
        string(name: 'SERVICE', description: 'The name of the service you want to deploy.'),
        string(name: 'SOURCE_IMAGE_TAG', defaultValue: 'latest', description: 'The source tag of the image you want to deploy.'),
	string(name: 'DEST_IMAGE_TAG', defaultValue: 'develop', description: 'The target tag of the image you want to deploy.'),
        choice(name: 'ENVIRONMENT', choices: ['development', 'staging', 'production'], description: 'Target environment you want to deploy to.')
     ])
])

node {
    def deploySuccessful = true
    try {
	stage ('Checkout') {
		checkout scm
	}

	def imageToDeploy = "${params.SERVICE}:${params.SOURCE_IMAGE_TAG}"
	print "Deploying ${imageToDeploy} to ${params.ENVIRONMENT}"

	stage ('Tag Image For Deployment') {
		openshift.withCluster() { // Use "default" cluster or fallback to OpenShift cluster detection
		    openshift.withProject("${params.ENVIRONMENT}") { // select namespace
		       openshift.tag("${imageToDeploy}", "${params.SERVICE}:${params.DEST_IMAGE_TAG}")    
                     }
		}
	}
	    
	stage ('Deploying to Prod environment') {
		openshift.withCluster() { // Use "default" cluster or fallback to OpenShift cluster detection
		    openshift.withProject("${params.ENVIRONMENT}") { // select namespace
		       openshift.newApp("hello-react:${params.DEST_IMAGE_TAG}", "--name=hello-react-prod").narrow('svc').expose() 
                     }
		}
	}
    } catch (e) {
        deploySuccessful = false
        throw e
    } finally {
	// send mail
    }
}
