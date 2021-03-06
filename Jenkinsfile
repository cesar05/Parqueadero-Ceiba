pipeline {
    //Donde se va a ejecutar el Pipeline
    agent {
        label 'Slave_Induccion'
    }
    //Opciones espec�ficas de Pipeline dentro del Pipeline
    options {
        //Mantener artefactos y salida de consola para el # espec�fico de ejecuciones recientes del Pipeline.
        buildDiscarder(logRotator(numToKeepStr: '3'))
        //No permitir ejecuciones concurrentes de Pipeline
        disableConcurrentBuilds()
    }
    //Una secci�n que define las herramientas para "autoinstalar" y poner en la PATH
    tools {
        jdk 'JDK8_Centos' //Preinstalada en la Configuraci�n del Master
        gradle 'Gradle4.5_Centos' //Preinstalada en la Configuraci�n del Master
    }
    //Aqu� comienzan los �items� del Pipeline
    stages{
        stage('Checkout') {
            steps{
                echo "------------>Checkout<------------"
				git branch: 'master', credentialsId: 'GitHub_cesar05', url: 'https://github.com/cesar05/Parqueadero-Ceiba.git'
				sh 'gradle clean'
            }
        }
        stage('Compile') {
			steps{
				echo "------------>Compile<------------"
				sh 'gradle --b ./build.gradle compileJava'
			}
		}
        stage('Unit Tests') {
            steps{
                echo "------------>Unit Tests<------------"
                //sh 'gradle --b ./build.gradle test --tests co.com.ceiba.parqueadero.unitaria.* -x installAngular -x buildAngular -x compileJava'
                sh 'gradle unitTest'
            }
        }
        stage('Integration Tests') {
            steps {
                echo "------------>Integration Tests<------------"
                sh 'gradle integrationTest'
                //sh 'gradle --b ./build.gradle test --tests co.com.ceiba.parqueadero.integracion.* -x installAngular -x buildAngular -x compileJava'
            }
        }
        stage('Functional Tests') {            
            steps {
            	echo "------------>Functional Tests<------------"  
            	sh 'chmod +x  libs/chromedriver'  
            	sh 'ls -la libs/'
            	sh 'pwd'
            	sh 'ls'            	
            	//sh 'cat build/reports/tests/test/classes/co.com.ceiba.parqueadero.funcionales.AppParqueadero.html'
                //sh 'gradle --b ./build.gradle test --tests co.com.ceiba.parqueadero.funcionales.* -x installAngular -x buildAngular -x compileJava'
            }
        }
        stage('Static Code Analysis') {
            steps{
            	//sh 'gradle --b ./build.gradle jacocoTestReport'
                echo '------------>An�lisis de c�digo est�tico<------------'
                withSonarQubeEnv('Sonar') {
					sh "${tool name: 'SonarScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'}/bin/sonar-scanner -Dsonar.organization=cesar05-github -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=8458aa38edc4b1be60aca74ce69202bd34130075"
				}
            }
        }
        stage('Build') {
            steps{
                    echo "------------>Build<------------"
                    //Construir sin tarea test que se ejecut� previamente
                    sh 'gradle --b ./build.gradle build -x test'
            }
        }
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
			//send notifications about a Pipeline to an email
			//mail (to: 'cesar.munoz@ceiba.com.co',
			//      subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
			//      body: "Something is wrong with ${env.BUILD_URL}")
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}