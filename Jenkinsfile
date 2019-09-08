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
  def SONAR_URL = 'http://172.17.0.3:9000'
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
