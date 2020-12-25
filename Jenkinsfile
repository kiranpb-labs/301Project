node {
try {
 emailext body: 'Pipeline triggered.', subject: 'Pipeline Job is triggered', to: 'kiranpb@gmail.com'
 stage('Git CheckOut') {
    git branch: 'main', url: 'https://github.com/kiranpb-labs/301Project.git'
}
 def project_path="petclinic-code"
 dir(project_path) {
 stage('Maven Clean') {
    sh 'mvn clean'
}
  stage('Maven Compile') {
    sh 'mvn compile'
}
 stage('Maven Testing') {
    sh 'mvn test'
}
 stage('Maven Package') {
    sh 'mvn package'
}
 stage('Archive Artifacts') {
    archive 'target/*.war'
}

   stage('Archive into Artifactory') {
rtUpload (
    serverId: '1',
    spec: '''{
          "files": [
            {
              "pattern": "**/*.war",
                       "target": "generic-snapshot/"

            }
         ]
    }''',
    buildName: 'petclinic',
    buildNumber: '1'
)
          rtPublishBuildInfo (
    serverId: '1',
     buildName: 'petclinic',
    buildNumber: '1'
)
}

         stage('SonarQube Code Check')
    {
        withSonarQubeEnv('SonarQube') {
                sh 'mvn clean package sonar:sonar'
        }
    }

 stage('Getting Ready For Ansible') {
    sh 'cp -rf target/*.war ../../03-Tomcat/files/'
    sh "echo '<h1> Task Build ID: ${env.BUILD_DISPLAY_NAME}</h1>' > ../../03-Tomcat/files/jenkins.html"
}

 }

def project_ansible="03-Tomcat/"
dir(project_ansible){

 stage('Ansible Deployment') {
    sh 'ansible-playbook webserver.yaml'
}

}
       } catch (err) {
     emailext body: "${err}", subject: 'Pipeline Failed!!', to: 'kiranpb@gmail.com'
       }
}