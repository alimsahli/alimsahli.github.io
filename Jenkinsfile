pipeline {
    // 1. Set the Docker agent for the whole pipeline
    // This provides a consistent environment with Ruby/Bundler pre-installed.
    agent any

    stages {

        /* ======================================================
            JEKYLL BUILD 
        ====================================================== */
        stage('Jekyll Build') {
            steps {
                // 2. Wrap the steps that need Ruby/Bundler inside docker.withRegistry/docker.image.inside
                script {
                    docker.image('ruby:3.2-slim').inside('-u root') {
                        sh '''
                        echo "--- Running inside Ruby Docker container ---"
                        gem install bundler || true
                        bundle install
                        bash tools/init.sh || true
                        JEKYLL_ENV=production bundle exec jekyll build --destination _site
                        '''
                    }
                }
            }
        }


        /* ======================================================
            SECRET SCAN (Gitleaks)
        ====================================================== */

        stage('SECRET SCAN (Gitleaks via Docker)') {
            steps {
                // This stage still uses its own ephemeral Docker container for Gitleaks.
                sh 'docker run --rm -v $WORKSPACE:/app -w /app zricethezav/gitleaks:latest detect --source=. --report-path=gitleaks-report.json --exit-code 0'
            }
        }

        /* ======================================================
            CHECK FOR FORBIDDEN .env
        ====================================================== */

        stage('Security Check: Forbidden .env File') {
            steps {
                script {
                    if (fileExists('.env')) {
                        error("🚨 FATAL SECURITY ERROR: The forbidden '.env' file was found!")
                    } else {
                        echo '✅ No forbidden .env file found.'
                    }
                }
            }
        }

        /* ======================================================
            SONARQUBE SCAN
        ====================================================== */

        stage('SONARQUBE SCAN') {
            // Note: Your Jenkins agent must have the 'sonar-scanner' command available,
            // or you must use a Docker container for this step too.
            environment {
                SONAR_HOST_URL = 'http://192.168.50.4:9000/'
                SONAR_AUTH_TOKEN = credentials('sonarqube')
            }
            steps {
                sh '''
                    sonar-scanner \
                     -Dsonar.projectKey=portfolio \
                     -Dsonar.projectName=portfolio \
                     -Dsonar.sources=. \
                     -Dsonar.host.url=$SONAR_HOST_URL \
                     -Dsonar.login=$SONAR_AUTH_TOKEN
                '''
            }
        }

        stage('QUALITY GATE WAIT (BLOCKING)') {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    echo 'Vérification du Quality Gate SonarQube en cours...'
                }
            }
        }

        /* ======================================================
            TRIVY SCAN (SCA)
        ====================================================== */

        stage('DEPENDENCY SCAN (SCA - Trivy via Docker)') {
            steps {
                script {
                    sh '''
                        docker run --rm \
                          -v $WORKSPACE:/app \
                          -w /app \
                          aquasec/trivy:latest \
                          fs --severity HIGH,CRITICAL --format json --output trivy-sca-report.json . || true
                    '''

                    sh '''
                        echo "--- TRIVY VULNERABILITY REPORT (HIGH/CRITICAL) ---"
                        docker run --rm \
                          -v $WORKSPACE:/app \
                          -w /app \
                          realguess/jq:latest \
                          jq -r '
                            .Results[] | select(.Vulnerabilities) | {
                                Target: .Target,
                                Vulnerabilities: [
                                    .Vulnerabilities[] 
                                    | select(.Severity == "CRITICAL" or .Severity == "HIGH") 
                                    | { Severity, VulnerabilityID, PkgName, InstalledVersion }
                                ]
                            }
                          ' trivy-sca-report.json
                    '''

                    echo 'Trivy scan finished.'
                }
            }
        }

        /* ======================================================
            DOCKER IMAGE CREATION (Nginx)
        ====================================================== */

        stage('IMAGE CREATION') {
            steps {
                echo "Building image alimsahlibw/portfolio:latest"
                // NOTE: The Jenkins agent (the host running the Ruby container) must have Docker installed here.
                sh 'docker build -t alimsahlibw/portfolio:latest .'
                sh 'docker image prune -f'
            }
        }

        /* ======================================================
            DOCKER HUB PUSH
        ====================================================== */

        stage('DOCKER HUB PUSH') {
            steps {
                sh 'docker tag alimsahlibw/portfolio:latest alimsahlibw/portfolio:${BUILD_NUMBER}'
                withCredentials([string(credentialsId: 'dockerhub', variable: 'DOCKERHUB_TOKEN')]) {
                    sh 'echo $DOCKERHUB_TOKEN | docker login -u alimsahlibw --password-stdin'
                    sh 'docker push alimsahlibw/portfolio:latest'
                    sh 'docker push alimsahlibw/portfolio:${BUILD_NUMBER}'
                }
            }
        }

        /* ======================================================
            OWASP ZAP BASELINE SCAN (DAST)
        ====================================================== */

        stage('OWASP ZAP SCAN (DAST)') {
            steps {
                script {
                    def appContainer
                    def targetUrl = "http://172.17.0.1:3000"  // Portfolio container runs on 3000

                    try {
                        echo "Starting portfolio container..."
                        appContainer = sh(
                            returnStdout: true,
                            script: "docker run -d -p 3000:80 alimsahlibw/portfolio:latest"
                        ).trim()

                        echo "Waiting for app to become ready..."
                        retry(5) {
                            sleep 5
                            sh "curl -s -o /dev/null ${targetUrl}"
                        }

                        echo "Running ZAP Baseline Scan..."
                        sh """
                            docker run --rm \
                              --network=host \
                              -v ${PWD}:/zap/wrk/:rw \
                              owasp/zap2docker-weekly:latest \
                              zap-baseline.py \
                                -t ${targetUrl} \
                                -r zap-report.html \
                                -x zap-report.xml \
                                -I
                        """

                    } catch (Exception e) {
                        echo "🚨 ZAP Error: ${e.getMessage()}"
                    } finally {
                        if (appContainer) {
                            echo "Cleaning up container..."
                            sh "docker stop ${appContainer}"
                            sh "docker rm ${appContainer}"
                        }
                    }
                }
            }
        }
    }

    /* ======================================================
        POST ACTIONS (EMAIL)
    ====================================================== */

    post {
        always {
            archiveArtifacts artifacts: 'trivy-sca-report.json,gitleaks-report.json,zap-report.html,zap-report.xml', allowEmptyArchive: true
        }
        success {
            emailext(
                subject: "✅ Pipeline SUCCESS: ${currentBuild.fullDisplayName}",
                body: """Hello,
                
                The portfolio DevSecOps pipeline **completed successfully**!
                """,
                to: "alimsahli.si@gmail.com",
                attachmentsPattern: 'trivy-sca-report.json,gitleaks-report.json,zap-report.html,zap-report.xml'
            )
        }
        failure {
            emailext(
                subject: "❌ Pipeline FAILED: ${currentBuild.fullDisplayName}",
                body: """Hello,
                
                The portfolio pipeline has failed. Please check attached reports.
                """,
                to: "alimsahli.si@gmail.com",
                attachmentsPattern: 'trivy-sca-report.json,gitleaks-report.json,zap-report.html,zap-report.xml'
            )
        }
    }
}