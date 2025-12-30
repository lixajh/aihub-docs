pipeline {
       agent {
        label 'dockerbuild'
    }

options {
        timeout(time: 1, unit: 'HOURS')  // <-- 在这里设置总超时时间
    }

    parameters{

         gitParameter branchFilter: 'origin/(.*)', defaultValue: 'master', name:'GIT_BRANCH',type:'PT_BRANCH_TAG' ,quickFilterEnabled: true
        choice(
            name: 'ENV',
            choices: ['dev', 'prod'],
            description: 'Select deployment environment. If prod, a latest tag will be added.'
        )

    }
    environment {
        GIT_URL = 'https://github.com/lixajh/aihub-docs.git'
        GIT_CREDENTIALS_ID = 'lx-github'  // 这里指定你的凭据 ID
        DOCKER_CREDENTIALS_ID = '14.129docker'
        DOCKER_REGISTRY = '192.168.14.129:80'
        DOCKER_NAME = "aihub-docs"
        // IMAGE_TAG = ""
        
    }

    stages{


        stage('Checkout Code') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: "${params.GIT_BRANCH}"]],
                        doGenerateSubmoduleConfigurations: false,
                       extensions: [
                            [$class: 'SubmoduleOption', 
                             recursiveSubmodules: true, 
                             trackingSubmodules: false, 
                             reference: '', 
                             timeout: 60000, 
                             shallow: false, 
                             noTags: false],

                            [$class: 'CloneOption',
                             shallow: true,   // 启用浅克隆
                             noTags: true,    // 不拉取 tags，加快速度
                             depth: 1],

                             [$class: 'CheckoutOption',
                             timeout:60000]
                        ],
                        userRemoteConfigs: [[
                            url: "${env.GIT_URL}",
                            credentialsId: "${env.GIT_CREDENTIALS_ID}"
                        ]]
                    ])
                    sh "ls -lat" // 列出工作目录中的文件，确认检出是否成功
                    // 获取当前 Git 提交号并更新 IMAGE_TAG
                    script {
                        def commitId = sh(script: "git rev-parse HEAD", returnStdout: true).trim()
                        echo "Current GIT_BRANCH: ${params.GIT_BRANCH}" 
                        if (params.GIT_BRANCH == 'dev' || params.GIT_BRANCH == 'test') {
                            // 取得前八个字符并打印
                            def shortCommitId = commitId.take(8)
                            env.IMAGE_TAG = shortCommitId
                            
                            // 打印短的 commit ID 和环境变量 IMAGE_TAG
                            echo "Short Commit ID: ${shortCommitId}"
                            echo "Image Tag: ${env.IMAGE_TAG}"
                        }else{
                            env.IMAGE_TAG = "${params.GIT_BRANCH}"
                        }
                        
                        sh "echo ${env.IMAGE_TAG}"
                        
                    }
                }
            }
        }



        stage('Docker Login') {
            steps {
                script {
                    // 使用 Jenkins 凭据执行 docker login
                    withCredentials([usernamePassword(credentialsId: "${env.DOCKER_CREDENTIALS_ID}", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login ${env.DOCKER_REGISTRY} -u $DOCKER_USERNAME --password-stdin"

                    }
                }
            }
        }

        stage('docker build') {
            steps {
                // 构建镜像的命令
                sh "echo ${env.IMAGE_TAG}"
                sh "docker build -t ${env.DOCKER_REGISTRY}/aied/aihub/${env.DOCKER_NAME}:${env.IMAGE_TAG}  ."
            }
        }
        stage('docker push') {
            steps {
                // 推送镜像的命令
                sh "docker push ${env.DOCKER_REGISTRY}/aied/aihub/${env.DOCKER_NAME}:${env.IMAGE_TAG}"

                script {
                    if (params.ENV == 'prod') {
                        sh "docker tag ${env.DOCKER_REGISTRY}/aied/aihub/${env.DOCKER_NAME}:${env.IMAGE_TAG} ${env.DOCKER_REGISTRY}/aied/aihub/${env.DOCKER_NAME}:latest"
                        h "docker push ${env.DOCKER_REGISTRY}/aied/aihub/${env.DOCKER_NAME}:latest"
                        h "docker rmi ${env.DOCKER_REGISTRY}/aied/aihub/${env.DOCKER_NAME}:latest"
                    }
                }

                sh "docker rmi ${env.DOCKER_REGISTRY}/aied/aihub/${env.DOCKER_NAME}:${env.IMAGE_TAG}"
            }
        }

        stage('Log Image Tag') {
            steps {
                script {
                    echo "Docker Image Tag: ${env.IMAGE_TAG}"
                }
            }
        }

    }


    post {
        success {
            script {
                // 检查参数的值，如果为特定值（如 'true'），则触发另一个 Pipeline
                if (params.GIT_BRANCH == 'dev') {
                    def deployJobName = env.JOB_NAME.replace("build", "deploy")
                    build job: deployJobName, 
                          parameters: [
                              string(name: 'DEPLOY_GIT_BRANCH', value:  "dev"),
                              string(name: 'CODE_BRANCH', value:  "dev"),
                              string(name: 'ENV', value: "dev"),
                              string(name: 'IMAGE_TAG', value: "${env.IMAGE_TAG}")
                          ]
                }
            }
        }
    }

}
