pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Baixando a aplicação e instalando dependências...'
                    sh '''
                        DEV_DIR=./dev
                        GIT_REPO=git@github.com:PyroPapyrus/API-livros-automatizacao.git
                        
                        if [ ! -d "$DEV_DIR" ]; then
                            echo "Criando pasta $DEV_DIR..."
                            mkdir "$DEV_DIR"
                        fi
                        
                        cd "$DEV_DIR"
                        git pull origin main
                        
                        python3 -m venv apiBooks
                        . apiBooks/bin/activate
                    '''
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    echo 'Realizando o deploy...'
                    sh '''
                        PROD_DIR=./prod
                        APP_PROCESS=apiBooksClass.py
                        
                        if [ ! -d "$PROD_DIR" ]; then
                            echo "Criando pasta $PROD_DIR..."
                            mkdir "$PROD_DIR"
                        fi
                        
                        if pgrep -f "$APP_PROCESS"; then
                            echo "Matando o processo $APP_PROCESS..."
                            pkill -f "$APP_PROCESS"
                        fi
                        
                        rm -rf "$PROD_DIR/*"
                        cp -r "$DEV_DIR/"* "$PROD_DIR/"
                        
                        cd "$PROD_DIR"
                        . ../dev/apiBooks/bin/activate
                        
                        nohup python3 apiBooksClass.py &
                    '''
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finalizado.'
        }
    }
}

