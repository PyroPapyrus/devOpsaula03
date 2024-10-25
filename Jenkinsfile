pipeline {
    agent any

    stages {
        // Estágio de Build
        stage('Build') {
            steps {
                script {
                    echo 'Baixando a aplicação e instalando dependências...'
                    // Baixando a aplicação do Git e instalando o ambiente virtual
                    sh '''
                    DEV_DIR="./dev"
                    GIT_REPO="git@github.com:PyroPapyrus/API-livros-automatizacao.git"
                    
                    # Criando a pasta dev se ela não existir
                    if [ ! -d "$DEV_DIR" ]; then
                        echo "Criando pasta $DEV_DIR..."
                        mkdir "$DEV_DIR"
                    fi
                    
                    # Baixando o código do repositório GIT
                    cd "$DEV_DIR"
                    git pull origin main
                    if [ $? -ne 0 ]; then
                        echo "Erro ao baixar a aplicação do GIT."
                        exit 1
                    fi
                    
                    # Criando ambiente virtual e instalando dependências
                    python3 -m venv apiBooks
                    source apiBooks/bin/activate
                    pip install -r requirements.txt
                    '''
                }
            }
        }

        // Estágio de Deploy
        stage('Deploy') {
            steps {
                script {
                    echo 'Iniciando o processo de Deploy...'
                    sh '''
                    DEV_DIR="./dev"
                    PROD_DIR="./prod"
                    VENV_DIR="$DEV_DIR/apiBooks"
                    APP_PROCESS="apiBooksClass.py"
                    
                    # Matando o processo da aplicação se estiver rodando
                    PID=$(pgrep -f "$APP_PROCESS")
                    if [ -n "$PID" ]; then
                        echo "Matando processo $APP_PROCESS com PID $PID..."
                        kill -9 "$PID"
                    fi
                    
                    # Limpando o diretório prod
                    if [ ! -d "$PROD_DIR" ]; then
                        mkdir "$PROD_DIR"
                    fi
                    rm -rf $PROD_DIR/*
                    
                    # Copiando os arquivos do dev para o prod
                    cp -r $DEV_DIR/* $PROD_DIR/
                    
                    # Ativando o ambiente virtual
                    cd $PROD_DIR
                    source $VENV_DIR/bin/activate
                    
                    # Iniciando a aplicação
                    nohup python3 $APP_PROCESS &
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

