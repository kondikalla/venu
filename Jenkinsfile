pipeline {
  agent any
  tools {nodejs "node"}

    stages {
        stage("Checkout") {
            steps {
                load "environmentVariables.groovy"
                echo "${env.DEV_SCM_REPOSITORY}"
                echo "${env.DEV_SCM_BRANCH}"
                git(url: "${env.DEV_SCM_REPOSITORY}", branch: "${env.DEV_SCM_BRANCH}", poll: true)
            }
        }

        stage("Build") {
        steps {
                echo "Building.."
                //sh 'mvn org.codehaus.mojo:exec-maven-plugin:exec'
               sh 'npm install'
            }
		}

        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }

        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
       }
        
        stage('Pack artefacts'){
            steps {
            script {
                def npmPack = sh(returnStdout:true, script:'npm pack').trim()
                env.npmPack = npmPack
            	sh "echo ${npmPack}"
            }   
            }
        }

    post {
        always {
          cleanWs() 
          
 
        }
        
        success{
            
                sh 'git commit "package.json" -m'
       
    
        }
    }