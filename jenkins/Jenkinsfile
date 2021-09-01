node {
 	// Clean workspace before doing anything
    deleteDir()

    try {
        stage ('Clone') {
        	checkout scm
        }
         stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: 'http://localhost:8082/artifactory',
                    credentialsId: 'admin_pradeep_artifactory'
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: 'local-repo-maven',
                    snapshotRepo: 'local-repo-maven'
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: 'local-repo-maven',
                    snapshotRepo: 'local-repo-maven'
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: MAVEN_TOOL, // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }
        stage ('Build') {
        	sh "echo 'shell scripts to build project...'"
        }
        stage ('Tests') {
	        parallel 'static': {
	            sh "echo 'shell scripts to run static tests...'"
	        },
	        'unit': {
	            sh "echo 'shell scripts to run unit tests...'"
	        },
	        'integration': {
	            sh "echo 'shell scripts to run integration tests...'"
	        }
        }
      	stage ('Deploy') {
            sh "echo 'shell scripts to deploy to server...'"
      	}
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}




