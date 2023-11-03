Pipeline{
    agent any

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