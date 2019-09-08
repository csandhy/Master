#!groovy
/*********************************************************************
***** Description :: This template is used to setup sample pipeline *****
***** Revision    :: 1.0                                         *****
**********************************************************************/  
import hudson.model.*
import hudson.EnvVars
import groovy.json.JsonSlurperClassic
import groovy.json.JsonBuilder
import groovy.json.JsonOutput
import java.net.URL


node {
   def GIT_URL = 'https://github.com/bhanuprakash678910/demo3.git'
   def GIT_BRANCH = 'master'
   def SONAR_BRANCH = 'master'
   def MAVEN_GOALS = 'clean install -X'
   def MAVEN_HOME = tool 'M2_HOME'
   def JAVA_HOME = tool 'JAVA_HOME'
   def SONAR_URL = 'http://3.14.141.108:9000/'
   def SONAR_LOGIN='admin'
   def SONAR_PASSWORD='admin'
   def artifactory_user='admin'
   def artifactory_password='admin'
   def dversion='$TAG'

   env.PATH = "${JAVA_HOME}/bin:${MAVEN_HOME}/bin:${env.PATH}"
   properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '5', artifactNumToKeepStr: '5', daysToKeepStr: '5', numToKeepStr: '5')), pipelineTriggers([])])
   properties([parameters([string(defaultValue: 'v1', description: 'Please choose version for tomcat docker image creation; default value is v1', name: 'TAG')]), pipelineTriggers([])])
   
   /****************************************
   *  Checkout code from GIT
   *****************************************/
   stage('\u2780 Checkout Code from GIT') 
   {
      
        echo "INFO => Checking out from URL: ${GIT_URL} and BRANCH: ${GIT_BRANCH}"
                   checkout([$class: 'GitSCM', branches: [[name: "*/${GIT_BRANCH}"]], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CloneOption', noTags: false, reference: '', shallow: true]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git-credentials', url: "${GIT_URL}"]]])
                
   }
    /*******************************************
   *  Build stage to trigger the maven build
   ********************************************/
   stage('\u2781 Compile and execute Unit test')
   {
      
        def ARTIFACT_VERSION = getVersion()
                   if (ARTIFACT_VERSION)
                   {
                     echo "INFO => Building Version: ${ARTIFACT_VERSION}"
                   }
                   echo "INFO => MAVEN_HOME: ${MAVEN_HOME}"
                   echo "INFO => JAVA_HOME : ${JAVA_HOME}"
                   sh "mvn -B ${MAVEN_GOALS} -DskipTests"
                 
   }
   stage('\u2782 Sonar Analysis and coverage')
   {
      
                   
                                  echo "INFO => Running Sonar Analysis"
                                             sh "mvn -DBranch=${SONAR_BRANCH} -e -B sonar:sonar -Dsonar.host.url=${SONAR_URL} -Dsonar.login=${SONAR_LOGIN} -Dsonar.password=${SONAR_PASSWORD} -Dsonar.scm.disabled=true -X"
                    
                 
   }
   stage('\u2783 Upload package on artifactory')
   {
     def ARTIFACT_VERSION = getVersion()
     echo ARTIFACT_VERSION
     String a = ARTIFACT_VERSION.replaceAll("-SNAPSHOT","")
     
    /*** sh 'b=`$ARTIFACT_VERSION| cut -f1 -d - `; echo $b' ***/
     echo "INFO => Deploying package to artifactory"
     sh "curl -u $artifactory_user:$artifactory_password -T $WORKSPACE/target/hello-world-war-${ARTIFACT_VERSION}.war http://3.14.141.108:8081/artifactory/generic-local/hello-world-war-${ARTIFACT_VERSION}.war"
                
   }
   stage('\u2784 Build Docker Image')
   {
       def ARTIFACT_VERSION = getVersion()
     echo ARTIFACT_VERSION
     String a = ARTIFACT_VERSION.replaceAll("-SNAPSHOT","")
     
     sh '''cd /var/lib/jenkins/workspace/projectdemo/docker
cp $WORKSPACE/target/hello-world-war-3.0.0.war .
 mv hello-world-war-3.0.0.war demo.war 
 docker build -t bhanu/tomcat:$TAG .
 docker run -d -p 8085:8080 bhanu/tomcat:$TAG '''
   }
  
}
def getVersion()
{
   def matcher = readFile("pom.xml") =~ '<version>(.+)</version>'
   matcher ? matcher[0][1] : null
}
