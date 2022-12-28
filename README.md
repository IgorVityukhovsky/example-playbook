# Домашнее задание к занятию "09.03 Jenkins"

Установил Jenkins, создал Freestyle Job, который будет запускать ansible-playbook из форка репозитория  
Тоже самое сделал с помощью декларативного пайплайна, записал его в Jenkinsfile и загрузил в репозиторий

Jenkinsfile получился вида
```
pipeline {
    agent any

    stages {
        stage('Git') {
            steps {
                sh 'git clone https://github.com/IgorVityukhovsky/example-playbook.git && cd example-playbook && ansible-playbook site.yml -e "ansible_become_password=123"'
            }
        }
        
    }
}
```
