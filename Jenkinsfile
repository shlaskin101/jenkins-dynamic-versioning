pipeline {
    agent any

    tools {
        maven 'maven-3.9'
    }

    environment {
        PROJECT_NAME = "java-maven-app"
        GITLAB_API_URL = "https://gitlab.com/api/v4/projects"
        PROJECT_ID = "70875470"
    }

    stages {

        stage('increment version') {
            steps {
                script {
                    echo 'incrementing app version...'
                    sh 'mvn build-helper:parse-version versions:set -DnewVersion=\\${parsedVersion.majorVersion}.\\${parsedVersion.minorVersion}.\\${parsedVersion.nextIncrementalVersion} versions:commit'
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "${version}-${BUILD_NUMBER}"
                }
            }
        }

        stage('build app') {
            steps {
                script {
                    echo 'building the application...'
                    sh 'mvn clean package'
                }
            }
        }

        stage('build image') {
            steps {
                script {
                    echo "building the docker image..."
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker build -t shlaskin/demo-app:${IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push shlaskin/demo-app:${IMAGE_NAME}"
                    }
                }
            }
        }

        stage('deploy') {
            steps {
                script {
                    echo 'deploying docker image...'
                    // Your deployment logic goes here
                }
            }
        }

        stage('commit version update and create merge request') {
            steps {
                script {
                    withCredentials([
                        usernamePassword(credentialsId: 'gitlab-credentials', usernameVariable: 'USER', passwordVariable: 'PASS'),
                        string(credentialsId: 'gitlab-api-token', variable: 'GITLAB_TOKEN')
                    ]) {
                        sh """
                            git config --global user.email "jenkins@example.com"
                            git config --global user.name "jenkins"
                            git remote set-url origin https://${USER}:${PASS}@gitlab.com/devops-bootcamp-2025/java-maven-app.git
                            git config pull.rebase false
                            git add .
                            git commit -m "ci: version bump" || echo "No changes to commit"
                            git push origin HEAD:refs/heads/ci/version-bump-${BUILD_NUMBER}

                            curl --request POST --header "PRIVATE-TOKEN: ${GITLAB_TOKEN}" \\
                                 --header "Content-Type: application/json" \\
                                 --data '{"source_branch": "ci/version-bump-${BUILD_NUMBER}", "target_branch": "master", "title": "ci: version bump"}' \\
                                 ${GITLAB_API_URL}/${PROJECT_ID}/merge_requests
                        """
                    }
                }
            }
        }
    }
}