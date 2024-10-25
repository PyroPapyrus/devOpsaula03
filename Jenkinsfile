pipeline {
    agent any

    environment {
        DEV_DIR = './dev'
        PROD_DIR = './prod'
        GIT_REPO = 'git@github.com:PyroPapyrus/API-livros-automatizacao.git'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Criar o diretório dev se não existir
                    if (!fileExists(DEV_DIR)) {
                        sh "mkdir -p ${DEV_DIR}"
                    }

                    // Baixar a aplicação do repositório GIT
                    dir(DEV_DIR) {
                        sh "git pull origin main || git clone ${GIT_REPO} ."
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Criar ambiente virtual
                    sh "python3 -m venv ${DEV_DIR}/apiBooks"
                    // Ativar o ambiente virtual
                    sh ". ${DEV_DIR}/apiBooks/bin/activate"
                    // Instalar dependências, se o requirements.txt estiver presente
                    if (fileExists("${DEV_DIR}/requirements.txt")) {
                        sh "pip install -r ${DEV_DIR}/requirements.txt"
                    } else {
                        echo "requirements.txt não encontrado. Pulando a instalação de dependências."
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Remover arquivos antigos da pasta prod
                    sh "rm -rf ${PROD_DIR}/*"
                    
                    // Copiar arquivos do diretório dev para prod
                    sh "cp -r ${DEV_DIR}/* ${PROD_DIR}/"

                    // Ativar o ambiente virtual e executar a aplicação
                    dir(PROD_DIR) {
                        sh ". ${DEV_DIR}/apiBooks/bin/activate && nohup python3 apiBooksClass.py &"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finalizado.'
        }
        failure {
            echo 'Ocorreu um erro durante o pipeline.'
        }
    }
}
