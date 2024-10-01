# Установка Docker

Docker - программная платформа для разработки, доставки и запуска контейнерных приложений.
Она позволяет создавать контейнеры, автоматизировать их запуск и развёртывание, управляет жизненным циклом.

Для установки Docker Engine вам потребуется 64-разрядная версия одной из следующих версий Ubuntu:
- Ubuntu Noble 24.04 (LTS)
- Ubuntu Jammy 22.04 (LTS)
- Ubuntu Focal 20.04 (LTS)

По скольку у нас имеется ansible, установка Docker возможна с использованием пары команд.
Также имеется возможность установки полностью в ручном режиме.

## Установка Docker с использованием ansible

Для автоматической установки docker необходимо скачать готовый репозиторий с конфигурациями, для этого
выполните следующие команды:

```Bash
sudo apt install git -y  # Если не установлен
git clone https://github.com/Daniel-Robotic/configuring_kuka_server.git
```

Далее необходимо отредактировать файл `host.ini`:
```Bash
nano configuring_kuka_server/ansible/hosts.ini
```

В нем содержиться следующая информация:

```
[server]
ubuntu_server ansible_host=127.0.0.1 ansible_port=22 ansible_user=user ansible_ssh_private_key_file=/home/user/.ssh/id_rsa

[jetson_nx]
jetson_left_wall ansible_host=127.0.0.1 ansible_port=22 ansible_user=user ansible_ssh_private_key_file=/home/user/.ssh/id_rsa>
jetson_front_wall ansible_host=127.0.0.1 ansible_port=22 ansible_user=user ansible_ssh_private_key_file=/home/user/.ssh/id_rsa>
jetson_lidar ansible_host=127.0.0.1 ansible_port=22 ansible_user=user ansible_ssh_private_key_file=/home/user/.ssh/id_rsa>

[jetson_nano]
jetson_kuka_tcp ansible_host=127.0.0.1 ansible_port=22 ansible_user=user ansible_ssh_private_key_file=/home/user/.ssh/id_rsa>
jetson_ceiling ansible_host=127.0.0.1 ansible_port=22 ansible_user=user ansible_ssh_private_key_file=/home/user/.ssh/id_rsa>
```

В `[server]`, замените ися пользователя хоста системы `ansible_user=user`, а также укажите путь до ключа `ssh`

> Для генерации `ssh` ключа воспользуйтесь на хостовой машине командой `ssh-keygen`, и выполните пункты, как было описано
> в [Подключение с использованием командной строки Windows](Настройка-подключения-к-Ubuntu-серверу-по-SSH-в-Windows.md)

> Вы также можете авторизироваться при помощи пароля, бля этого уберите `ansible_ssh_private_key_file` и замените его
> на `ansible_password=ваш_пароль`
> {style="note"}

Далее запустить `ansible-playbook`, для этого воспользуйтесь командой:
```Bash
ansible-playbook -i ./configuring_kuka_server/ansible/hosts.ini ./configuring_kuka_server/ansible/docker-server-install-playbook.yml
```

В начале выполнения вы должны получить три ошибки, а также в конце будет выведен результат работы `ansible`:

```Bash
PLAY RECAP *************************************************************************************************************
jetson_front_wall          : ok=0    changed=0    unreachable=1    failed=0    skipped=0    rescued=0    ignored=0
jetson_left_wall           : ok=0    changed=0    unreachable=1    failed=0    skipped=0    rescued=0    ignored=0
jetson_lidar               : ok=0    changed=0    unreachable=1    failed=0    skipped=0    rescued=0    ignored=0
ubuntu_server              : ok=13   changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Важно чтобы `ubuntu` сервер выполнился без ошибок.

Убедитесь, что вы можете выполнять команды `docker` без `sudo`:
```Bash
docker run hello-world
```

Эта команда загружает тестовый образ и запускает его в контейнере.
Когда контейнер запускается, он выводит сообщение и завершает работу.

Более подробно, про работу с инструментом `ansible` можно прочитать [тут](Ansible.md)


### Установка с помощью репозитория apt

Прежде чем впервые установить Docker Engine на новый хост-компьютер, необходимо настроить репозиторий Docker.
После этого вы можете установить и обновить Docker из репозитория.

Настройте репозиторий `apt` в Docker:

```Bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

> Если вы используете дистрибутив, производный от Ubuntu, такой как Linux Mint,
> вам, возможно, придется использовать `UBUNTU_CODENAME` вместо `VERSION_CODENAME`.
> {style="note"}

Чтобы установить последнюю версию, выполните:
```Bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Далее необходимо настроить группу пользователей.
Чтобы создать группу `Docker` и добавить пользователя, выполните следующие действия:

```Bash
sudo groupadd docker
sudo usermod -aG docker $USER
sudo systemctl restart docker
```

Убедитесь, что вы можете выполнять команды `docker` без `sudo`:
```Bash
docker run hello-world
```

Эта команда загружает тестовый образ и запускает его в контейнере.
Когда контейнер запускается, он выводит сообщение и завершает работу.