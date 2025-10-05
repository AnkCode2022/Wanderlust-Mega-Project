pipeline {
    agent any
    environment {
        SONAR_HOME = tool "sonar1"
    }
    stages {
        stage("Code") {
            steps {
                echo "Stage 1: Cloning the repository"
                git url: "https://github.com/AnkCode2022/Wanderlust-Mega-Project.git", branch: "main"
                echo "Git clone successful"
            }
        }
        stage("SonarQube Analysis") {
            steps {
                echo "Stage 2: SonarQube Scan"
                script {
                    withSonarQubeEnv("sonar1") {
                        sh """
                            ${SONAR_HOME}/bin/sonar-scanner \
                                -Dsonar.projectKey=Wanderlust \
                                -Dsonar.projectName=Wanderlust \
                                -Dsonar.sources=.
                        """
                    }
                    // Wait for SonarQube Quality Gate result
                    timeout(time: 2, unit: 'MINUTES') {
                        waitForQualityGate abortPipeline: true
                    }
                }
            }
        }
        stage("OWASP Dependency Check") {
            steps {
                echo "Stage 3 OWASP dependency check"
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: "dc"
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Sonar Quality gate scan"){
            steps{
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan"){
            steps{
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deploy through Docker"){
            steps{
                sh "docker-compose up -d"
            }
        }
    }
}
