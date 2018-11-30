@Library('github.com/fabric8io/osio-pipeline@master') _
def utils = new io.openshift.Utils()

osio {

  config runtime: 'java', version: '1.8.1'

  ci {
    echo "Running CI build............."


    def resources = processTemplate(params: [
          release_version: "1.0.${env.BUILD_NUMBER}"
    ])
    integrationTestCmd = "mvn clean verify -Dnamespace.use.current=false -Dnamespace.use.existing=${utils.usersNamespace()} -Popenshift,openshift-it" 
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
