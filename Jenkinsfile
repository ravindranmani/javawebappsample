import groovy.json.JsonSlurper

node {
  withEnv([
    'AZURE_SUBSCRIPTION_ID=d9e25859-f02c-40e8-8f9d-a27696ab41cc',
    'AZURE_TENANT_ID=60a402fe-9582-4148-9b8d-590ff2eb8a99'
  ]) {

    stage('init') {
      checkout scm
    }

    stage('build') {
      sh 'mvn clean package'
    }

    stage('deploy') {
      def resourceGroup = 'jenkins-get-started-rg'
      def webAppName = 'jenkins-get-started-app-mani'

      // Login to Azure using service principal
      withCredentials([usernamePassword(
        credentialsId: 'AzureServicePrincipal',
        usernameVariable: 'AZURE_CLIENT_ID',
        passwordVariable: 'AZURE_CLIENT_SECRET'
      )]) {
        sh '''
          az login --service-principal \
            -u $AZURE_CLIENT_ID \
            -p $AZURE_CLIENT_SECRET \
            -t $AZURE_TENANT_ID

          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
      }

      // Deploy WAR using Azure CLI (Linux-supported)
      sh """
      az webapp deploy \
        --resource-group jenkins-get-started-rg \
        --name jenkins-get-started-app-mani \
        --src-path /var/lib/jenkins/workspace/MyFirstPipeline/target/calculator-1.0.war \
        --type war \
        --target-path webapps/calculator-1.0.war
      """
      sh 'az logout'
    }
  }
}
