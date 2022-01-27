# Тестовое задание

- Создать Docker-контейнер с “hello world” приложением на node-js
- Настроить плейбук в Ansible, который разворачивает на сервере Docker и запускает настроенный контейнер

Требования к контейнеру:

- В нём не должно быть вообще ничего кроме бинарников, нужных для запуска node-js приложения
- Контейнер должен работать на базе Alpine Linux

# Решение

### Что получилось

[![ASCIInema preview](https://asciinema.org/a/yplz7Srsf3eMCYLTjEaKIDGKg.png)](https://asciinema.org/a/yplz7Srsf3eMCYLTjEaKIDGKg)

### Общее описание

Подразумевается, что на рабочем ПК установлен `ansible` версии 2.4.2 или совместимой.

В качестве хостера использую DigitalOcean, для работы с ним создана пара ключей в `files/keys`. Потребуется установка python-модуля `dopy`.

Приложение упаковывается в контейнер просто как исходный текст, запускаемый через `node main.js` из образа `node:8-alpine`. Итоговый образ весит порядка 70 МБ. Это [базовый образ alpine + node.js](https://github.com/nodejs/docker-node/blob/4e5eda8fedf7fa355de6037f01d5699d414c1da3/8/alpine/Dockerfile) + исходный код, ничего лишнего.

В дальнейшем образ предлагается ужать ещё больше, оставив только исполняемый файл nodejs, требуемые библиотеки и исходный код. Это было проделано [таким образом](https://agrrh.com/posts/2017/strip-docker-container/).

Сам исходный код приложения и Dockerfile находятся в [files/app/](https://github.com/agrrh/shy-exam/blob/master/files/app/).

Для непосредственной работы с плейбуком имеются несколько флоу, разделенных тэгами:

- `setup` - добавление ключа в аккаунт DO, создание хоста, установка Docker-а и прочего ПО на хост
- `deploy` - получение IP-адреса хоста, создание Docker-образа, запуск контейнера
- `clean` - удаление хоста, удаление ключа из аккаунт DO

# Использование

### Подготовка

Клонируем репо, устанавливаем сопутствующее ПО и модули, роли:

```
git clone git@github.com:agrrh/shy-exam.git
cd shy-exam

pip install -r requirements.txt

ansible-galaxy install -r roles/requirements.yml
```

В файле `vars/digitalocean_secret.yml` задаём API token для доступа к аккаунту DigitalOcean:

```
do_api_token: <your-token-here>
```

### Деплой

```
ansible-playbook shy-exam.yml -t setup,deploy,check
```

### Прибираем за собой

```
ansible-playbook shy-exam.yml -t cleanup
```
