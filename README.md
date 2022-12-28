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
Так же всё получилось с использованием его из репозитория  
Создал ScriptedJenkinsfile, наполнил его скриптом из ДЗ  
На ноду добавил label "ansible_docker" что бы задача запускалась  
Заменил credentialsId на собственные, выложил файл ScriptedJenkinsfile в репозиторий
С учетом правок для устранения ошибок привёл его к виду:
```
node("ansible_docker"){
    stage("Git checkout"){
        git credentialsId: 'igor', url: 'git@github.com:aragastmatb/example-playbook.git'
    }
    stage("Check ssh key"){
        secret_check=true
    }
    stage("Run playbook"){
        if (secret_check){
            sh 'ansible-playbook site.yml -i inventory/prod.yml -e "ansible_become_password=123"'
        }
        else{
            echo 'no more keys'
        }
        
    }
}
```
