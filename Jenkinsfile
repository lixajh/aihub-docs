pipeline {
       agent {
        label 'dockerbuild'
    }

options {
        timeout(time: 1, unit: 'HOURS')  // <-- 在这里设置总超时时间
    }
    
    parameters{

         gitParameter branchFilter: 'origin/(.*)', defaultValue: 'master', name:'GIT_BRANCH',type:'PT_BRANCH_TAG' ,quickFilterEnabled: true

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

        // stage('Initialize') {
        //     steps {
        //         script {
        //             // 定义一个字典，基于 JOB_NAME 存储不同的配置
        //             def configMap = [
        //                 'aihub-build-dw-server': [DOCKER_NAME: 'data_warehouse_server', DOCKERFILE_PATH: 'app/data_warehouse/deploy/Dockerfile'],
        //                 'aihub-build-dataset': [DOCKER_NAME: 'dataset-server', DOCKERFILE_PATH: 'app/dataset_management/deploy/Dockerfile'],
        //                 'aihub-build-dataset-import-meta': [DOCKER_NAME: 'import-meta', DOCKERFILE_PATH: 'app/dataset_management/script/import_meta/Dockerfile'],
        //                 'aihub-build-dataset-load-dataset': [DOCKER_NAME: 'load-dataset', DOCKERFILE_PATH: 'app/dataset_management/script/load_dataset/Dockerfile'],
        //                 'aihub-build-dataset-upload': [DOCKER_NAME: 'dataset-upload-server', DOCKERFILE_PATH: 'app/s3_upload_server/deploy/Dockerfile'],
        //                 'aihub-build-document-center': [DOCKER_NAME: 'document_center_server', DOCKERFILE_PATH: 'app/document_center/deploy/Dockerfile'],
        //                 'aihub-build-image-management': [DOCKER_NAME: 'image-management', DOCKERFILE_PATH: 'app/image_management/deploy/Dockerfile'],
        //                 'aihub-build-message-center': [DOCKER_NAME: 'message_center_server', DOCKERFILE_PATH: 'app/message_center/deploy/Dockerfile'],
        //                 'aihub-build-deployment-platform': [DOCKER_NAME: 'mldp-server', DOCKERFILE_PATH: 'app/deployment_platform/deploy/Dockerfile'],
        //                 'aihub-build-model-training': [DOCKER_NAME: 'mtp-server', DOCKERFILE_PATH: 'app/model_training_platform/deploy/Dockerfile'],
        //                 'aihub-build-pipeline-monitor': [DOCKER_NAME: 'pipeline-monitor', DOCKERFILE_PATH: 'app/pipeline_monitor_alert/deploy/Dockerfile'],
        //                 'test-build': [DOCKER_NAME: 'pipeline-monitor', DOCKERFILE_PATH: 'app/pipeline_monitor_alert/deploy/Dockerfile'],
        //                 'aihub-build-tag-resource-management': [DOCKER_NAME: 'tag-resource-management', DOCKERFILE_PATH: 'app/tag_resource_management/deploy/Dockerfile'],
        //                 'aihub-build-task-center': [DOCKER_NAME: 'task_center_server', DOCKERFILE_PATH: 'app/task_center/deploy/Dockerfile'],
        //                 'aihub-build-user-system': [DOCKER_NAME: 'user-system-server', DOCKERFILE_PATH: 'app/user_system/deploy/Dockerfile'],
        //                 'labelfree-build-labelfreev2': [DOCKER_NAME: 'labelfreev2', DOCKERFILE_PATH: 'app/labelfree/deploy/Dockerfile'],
        //                 'labelfree-build-labelfree-core': [DOCKER_NAME: 'labelfree-core', DOCKERFILE_PATH: 'app/labelfree/deploy/Dockerfile'],
        //                 'labelfree-build-labelfree-dispatch': [DOCKER_NAME: 'labelfree-dispatch', DOCKERFILE_PATH: 'app/labelfree/deploy/Dockerfile.rpc'],
        //                 'aihub-build-user-system': [DOCKER_NAME: 'user-system-server', DOCKERFILE_PATH: 'app/user_system/deploy/Dockerfile'],
        //                 'aihub-build-log-instrumentation': [DOCKER_NAME: 'log-instrumentation', DOCKERFILE_PATH: 'app/log_instrumentation/deploy/Dockerfile'],
        //                 'aihub-build-workflow-center': [DOCKER_NAME: 'workflow-center', DOCKERFILE_PATH: 'app/workflow_center/deploy/Dockerfile'],
        //                 'aihub-build-llm-bench': [DOCKER_NAME: 'llm-bench', DOCKERFILE_PATH: 'app/llm_bench/deploy/Dockerfile'],
        //                 'aihub-build-artifact-management': [DOCKER_NAME: 'artifact-management', DOCKERFILE_PATH: 'app/artifact_management/deploy/Dockerfile'],
        //                 'aihub-build-eval-platform': [DOCKER_NAME: 'eval-platform', DOCKERFILE_PATH: 'app/eval_platform/deploy/Dockerfile'],
        //                 'aihub-build-model-center': [DOCKER_NAME: 'model-center', DOCKERFILE_PATH: 'app/model_center/deploy/Dockerfile'],
        //                 'aihub-build-quota-schedule-management': [DOCKER_NAME: 'quota-schedule-management', DOCKERFILE_PATH: 'app/quota_schedule_management/deploy/Dockerfile'],
        //                 'aihub-build-dashboard-center': [DOCKER_NAME: 'dashboard-center', DOCKERFILE_PATH: 'app/dashboard_center/deploy/Dockerfile'],
        //                 'aihub-build-k8s-web-terminal': [DOCKER_NAME: 'k8s-web-terminal', DOCKERFILE_PATH: 'app/k8s_web_terminal/deploy/Dockerfile']
        //             ]

        //             // 获取当前 JOB_NAME 的配置
        //             def currentConfig = configMap.get(env.JOB_NAME)

        //             // 检查字典中是否有相应的配置
        //             if (currentConfig == null) {
        //                 error "No configuration found for JOB_NAME: ${env.JOB_NAME}"
        //             }

        //             // 使用从字典中获取的配置
        //             env.DOCKER_NAME = currentConfig.DOCKER_NAME
        //             env.DOCKERFILE_PATH = currentConfig.DOCKERFILE_PATH

                    
        //         }
        //     }
        // }

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
                             timeout: 10, 
                             shallow: false, 
                             noTags: false]
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
