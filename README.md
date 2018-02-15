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

Исходный код приложения находится в `files/app`.

Для непосредственной работы с плейбуком имеются несколько флоу, разделенных тэгами:

- `setup` - добавление ключа в аккаунт DO, создание хоста, установка Docker-а и прочего ПО на хост
- `deploy` - получение IP-адреса хоста, создание Docker-образа, запуск контейнера
- `clean` - удаление хоста, удаление ключа из аккаунт DO

# Использование

### Подготовка

Клонируем репо, устанавливаем сопутствующее ПО и модули, роли:

```
# Clone app and deploy scripts
git clone git@github.com:agrrh/cindicator-exam.git
cd cindicator-exam

# Install Ansible and related modules
pip install -r requirements.txt

# Install roles
ansible-galaxy install -r roles/requirements.yml
```

В файле `vars/digitalocean_secret.yml` задаём API token для доступа к аккаунту DigitalOcean:

```
# vars/digitalocean_secret.yml
do_api_token: <your-token-here>
```

### Деплой

```
# Deploy app
ansible-playbook cindicator-exam.yml -t setup,deploy,check

# Cleanup when done
ansible-playbook cindicator-exam.yml -t cleanup
```
