pipeline {
    agent any 

    stages {
        stage('Git') { 
            steps { 
                git branch:  params.branch, credentialsId: params.credId, url: params.GitUrl
            }
        }

        stage('写入配置') {
          steps {
            sh '''
              ''' + params.WriteFile + '''
            '''
          }
        }
        
        stage('构建镜像') { 
            steps {
               sh '''
                 docker build -t ${origin_repo}/account/${repo}:${image_tag} .
               '''
            }
        }

        stage('停止旧容器') {
            steps {
                sh '''
                  last_cid=$(docker ps | grep ${repo}-${image_tag} | awk '{print $1}')
                  if [[ -n $last_cid ]]
                  then
                    docker stop $last_cid
                    docker rm $last_cid
                  fi
                '''
            }
        }
        
        stage('启动容器') { 
            steps {
              sh '''
                docker run -d -p ${out_ip}:${in_ip} --name ${repo}-${image_tag} ${origin_repo}/account/${repo}:${image_tag}
              '''
            }
        }

        stage('清理镜像 ') {
            steps {
                sh '''
                  docker system prune -f
                '''
            }
        }
        
    }
}