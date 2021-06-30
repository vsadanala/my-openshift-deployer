properties([
     parameters([
        string(name: 'SERVICE', description: 'The name of the service you want to deploy.'),
        string(name: 'IMAGE_TAG', defaultValue: 'develop', description: 'The tag of the image you want to deploy.'),
        choice(name: 'ENVIRONMENT', choices: ['develop', 'staging', 'production'], description: 'Target environment you want to deploy to.')
     ])
])

node {
    def deploySuccessful = true
    try {
	stage ('Checkout') {
		checkout scm
	}

	def imageToDeploy = "${params.SERVICE}:${params.IMAGE_TAG}"
	print "Deploying ${imageToDeploy} to ${params.ENVIRONMENT}"

	stage ('Tag Image For Deployment') {
		openshift.withCluster() { // Use "default" cluster or fallback to OpenShift cluster detection
                    openshift.withProject('another-project') { // select namespace
		       openshift.tag("${imageToDeploy}", "${params.SERVICE}:${params.ENVIRONMENT}")
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
