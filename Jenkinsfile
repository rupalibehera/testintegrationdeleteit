@Library('github.com/rupalibehera/osio-pipeline@pod_template') _
def utils = new io.openshift.Utils()

osio {

  config runtime: 'java', version: '1.8.1'

  ci {
    echo "Running CI build............."


    def resources = processTemplate(params: [
          release_version: "1.0.${env.BUILD_NUMBER}"
    ])
    integrationTestCmd = "mvn org.apache.maven.plugins:maven-failsafe-plugin:integration-test \
            org.apache.maven.plugins:maven-failsafe-plugin:verify \
            -Dnamespace.use.current=false -Dnamespace.use.existing=${utils.usersNamespace()} \
            -Dit.test=*IT -DfailIfNoTests=false -DenableImageStreamDetection=true \
            -P openshift-it"
    //integrationTestCmd = "mvn clean verify -Dnamespace.use.current=false -Dnamespace.use.existing=${utils.usersNamespace()} -Popenshift,openshift-it" 
    //build resources: resources
    spawn commands: integrationTestCmd, image: 'java'
    

  }

  cd {
    echo "Running CD build......"

    def resources = processTemplate(params: [
          release_version: "1.0.${env.BUILD_NUMBER}"
    ])

    build resources: resources
    deploy resources: resources, env: 'stage'
    deploy resources: resources, env: 'run', approval: 'manual'

  }
}
