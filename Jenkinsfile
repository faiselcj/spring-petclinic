pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SONAR = 'SonarQube'
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
               git branch: 'main', url: 'https://github.com/faiselcj/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests -Dcheckstyle.skip=true'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                sh 'mvn deploy -s /var/lib/jenkins/.m2/settings.xml -DskipTests -Dcheckstyle.skip=true'
                }
            }
        stage('Deploy Application') {
            steps {
                sh '''
                echo "Stopping existing app..."
                pkill -f spring-petclinic || true

                echo "Starting application..."
                nohup java -jar target/*.jar > app.log 2>&1 &
                '''
            }
        }
    }
}
