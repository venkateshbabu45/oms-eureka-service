pipeline {
  agent any
  parameters {
    string(name: 'EUREKA_IPADDRESS', description: 'localhost')
  }
  tools { 
        maven 'Maven'
  }
  stages {
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace... */
      steps {
        checkout scm
      }
    }
    stage('Build') {
      steps {
        sh 'sed -i "s/localhost/$EUREKA_IPADDRESS/g" $WORKSPACE/src/main/resources/application.properties'
        sh 'mvn -B -DskipTests clean package'
        sh 'echo $USER'
        sh 'echo whoami'
      }
    }
   stage('CreateInstance') {
      steps {
        ansiblePlaybook credentialsId: 'aws-credentials', installation: 'Ansible', playbook: '$WORKSPACE/createInstance.yaml'
      }
   }
    stage('DeployArtifact') {
      steps {
        ansiblePlaybook become: true, credentialsId: 'aws-credentials', installation: 'Ansible', inventory: '/tmp/hosts_customer', playbook: '$WORKSPACE/deployArtifact.yaml'
      }
   }
  }
}
