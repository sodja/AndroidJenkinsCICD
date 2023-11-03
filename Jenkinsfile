Pipeline{
    agent any
    environment {
        APP_ARCHIVE_NAME = 'app'
        APP_MODULE_NAME = 'android-template'
        CHANGELOG_CMD = 'git log --date=format:"%Y-%m-%d" --pretty="format: * %s% b (%an, %cd)" | head -n 10 > commit-changelog.txt'
    }
    stages {
         stage("Build") {
                            steps {
                                sh 'chmod +x gradlew'
                                sh './gradlew -x clean assembleDebug'
                            }
           }
         stage("Test") {
                             steps {
                                 sh './gradlew -x clean testDebugUnitTest'
                             }
                         }
                         stage("AndroidTest") {
                             steps {
                                 sh './gradlew assembleDebugAndroidTest'
                             }
                         }
                         stage("build & SonarQube analysis") {
                             steps {
                                 withSonarQubeEnv('sonarqube') {
                                     sh 'chmod +x gradlew'
                                         sh './gradlew sonar'
                                 }
                             }
         }
          stage("Quality Gate") {
                              steps {
                                 script{
                                     timeout(time: 2, unit: 'MINUTES') {
                                         def qualitygate = waitForQualityGate()
                                         if (qualitygate.status != "OK") {
                                             error "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}"
                                         }
                                     }

                                 }
                              }
          }
           stage("Build release ") {
                      steps{
                          sh './gradlew assembleRelease'
                      }
          }
           stage("Compile") {
                      steps{
                                      archiveArtifacts artifacts: '**/*.apk', fingerprint: true, onlyIfSuccessful: true
                      }
                  }
    }
}