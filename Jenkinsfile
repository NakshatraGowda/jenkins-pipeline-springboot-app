pipeline {
    agent any

    tools{
        jdk 'JDK21'
        maven 'mvn3'
        // dependencyCheck 'DP'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        //JAVA_HOME = '/usr/lib/jvm/java-21-openjdk-amd64'
        //PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/NakshatraGowda/hello-world-springboot.git'
            }
        }
         stage('mvn') {
            steps {
                sh '''
                cd demo
                mvn clean compile
                '''
            }
        }
         stage('sonarqube analysis') {
            steps {
                sh '''
                $SCANNER_HOME/bin/sonar-scanner -Dsonar.host.url=http://13.54.230.146:9000/ -Dsonar.login=squ_e527c6918ab9598be4e28f19d89a971c62ef762a \
                -Dsonar.projectName=hello-world-springboot \
                -Dsonar.projectKey=hello-world-springboot \
                -Dsonar.java.binaries=. \
                '''
            }
        }
        //stage('Dependency Check') {
            //steps {
                //dependencyCheck additionalArguments: '--scan . --format XML', odcInstallation: 'DP'
            //}
        //}
        stage('Build Application') {
            steps {
                sh '''
                cd demo
                mvn clean install
                '''
            }
        }
        stage ('Build and push docker image'){
            steps {
                script{
                    dir('demo'){
                withDockerRegistry(credentialsId: 'dockerhub-creds', url: 'https://index.docker.io/v1/') {
                        sh '''
                            docker build -t hello-world-springboot-latest .
                            docker tag hello-world-springboot-latest nakshatragowda/hello-world-springboot-latest
                            docker push nakshatragowda/hello-world-springboot-latest
                        '''
                    }
                }
                }
                }
            }
        }
    }

