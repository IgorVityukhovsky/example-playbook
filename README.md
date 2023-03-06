# Домашнее задание к занятию "09.03 Jenkins"

## Подготовка к выполнению

1. Установить jenkins по любой из [инструкций](https://www.jenkins.io/download/)
2. Запустить и проверить работоспособность
3. Сделать первоначальную настройку
4. Настроить под свои нужды
5. Поднять отдельный cloud
6. Для динамических агентов можно использовать [образ](https://hub.docker.com/repository/docker/aragast/agent)
7. Обязательный параметр: поставить label для динамических агентов: `ansible_docker`
8.  Сделать форк репозитория с [playbook](https://github.com/aragastmatb/example-playbook)

## Основная часть

1. Сделать Freestyle Job, который будет запускать `ansible-playbook` из форка репозитория
2. Сделать Declarative Pipeline, который будет выкачивать репозиторий с плейбукой и запускать её
3. Перенести Declarative Pipeline в репозиторий в файл `Jenkinsfile`
4. Перенастроить Job на использование `Jenkinsfile` из репозитория
5. Создать Scripted Pipeline, наполнить его скриптом из [pipeline](./pipeline)
6. Заменить credentialsId на свой собственный
7. Проверить работоспособность, исправить ошибки, исправленный Pipeline вложить в репозитрий в файл `ScriptedJenkinsfile`
8. Отправить ссылку на репозиторий в ответе

## Необязательная часть

1. Создать скрипт на groovy, который будет собирать все Job, которые завершились хотя бы раз неуспешно. Добавить скрипт в репозиторий с решеним с названием `AllJobFailure.groovy`
2. Установить customtools plugin
3. Поднять инстанс с локальным nexus, выложить туда в анонимный доступ  .tar.gz с `ansible`  версии 2.9.x
4. Создать джобу, которая будет использовать `ansible` из `customtool`
5. Джоба должна просто исполнять команду `ansible --version`, в ответ прислать лог исполнения джобы 

---


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
Заменил credentialsId на собственные (задавал своё имя при создании кредов), выложил файл ScriptedJenkinsfile в репозиторий  
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
