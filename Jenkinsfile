pipeline {
    agent any

    stages {
        stage('github clone') {
            steps {
               git branch: 'main', credentialsId: 'wolfbitzkim', url: 'https://github.com/wolfbitzkim/springboot-gradle-demo.git'
            }
        }
        stage('build') {
            steps {
               sh '''
                echo 'start bootJar'
                chmod +x gradlew
                ./gradlew clean bootJar
                '''
            }
        }
        stage('deploy'){
            steps {
                sshPublisher(
                    publishers: [sshPublisherDesc(
                    configName: 'app-server',
                    transfers: [sshTransfer(
                        cleanRemote: false,
                        excludes: '',
                        execCommand: '''echo "> demo 실행 확인"
                                        CURRENT_PID=$(ps -ef | grep java | grep demo | grep -v nohup | awk \'{print $2}\')
                                        echo "$CURRENT_PID"
                                        if [ -z ${CURRENT_PID} ] ;then
                                        echo "> 현재 구동중인 애플리케이션이 없으므로 종료하지 않습니다."
                                        else
                                        echo "> sudo kill -9 $CURRENT_PID"
                                        sudo kill -9 $CURRENT_PID
                                        sleep 5
                                        fi
                                        echo "> JenkinsStudy 배포"
                                        JAR_PATH=$(ls -t *.jar | head -1)
                                        sudo nohup java -jar ${JAR_PATH} > nohup.out 2>&1 &''',
                                        execTimeout: 120000,
                                        flatten: false,
                                        makeEmptyDirs: false,
                                        noDefaultExcludes: false,
                                        patternSeparator: '[, ]+',
                                        remoteDirectory: '.',
                                        remoteDirectorySDF: false,
                                        removePrefix: 'build/libs', sourceFiles: 'build/libs/*.jar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

            }
        }
    }
}
