/* 
    To build and deploy on Azure please ensure the following:
    1) An Ubuntu 14.04 slave with the label "linux". Must have JDK 7 and Git installed
    2) Set up a Maven tool installer called "maven-3.3"
    3) Set up environment variables for:
         azureHost : the host to use for deployment - from the Azure console
         svchost : the public DNS name the app will be visible on
    4) Credentials with the id of "azure-deployment-id" containing the ftps user:password for deployment

*/

// test line
// test line 2
// test line 3
// test line 4

node ("linux") {

    def local_path="gameoflife-web/target"
    def war="gameoflife.war"
    def target="/site/wwwroot/bin/jetty-distribution-9.1.2.v20140210/webapps"
    //d:\home\site\wwwroot\bin\jetty

    ensureMaven()

    stage "Checkout"
    git branch: 'azure-pipeline', url: 'https://github.com/groverboy/game-of-life-azure-pipeline'

    stage "Build"

    sh "mvn clean package"
    
    stage "Deploy to Azure"

    withCredentials([[$class: 'UsernamePasswordMultiBinding', 
        credentialsId: 'azure-deployment-id', 
        passwordVariable: '_password', 
        usernameVariable: '_user']]) {

        def deploycmd = "curl -T ${local_path}/${war} ftps://\"${env._user}\":\"${env._password}\"@${env.azureHost}${target}/"
        echo deploycmd
        sh deploycmd
    }
    
    stage "Verify deployment"
    
    retry(count: 5) { 
        echo "Checking for the application at ${env.svchost}/gameoflife"
        sh "sleep 5 && curl -f ${env.svchost}/gameoflife"
    }

}

def ensureMaven() {
    env.PATH = "${tool 'maven-3.3'}/bin:${env.PATH}"
}
