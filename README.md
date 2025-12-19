# lab3
# Бикентаева Сауле ПИ-430Б
# Задание 1: Базовое подключение
УстановиЛА Ansible на компьютер
Сгенерировала SSH ключевую пару
Создала инвентарный файл inventory.ini
Проверила подключение командой ansible-inventory --list
Выполнила ping к управляемому хосту
![1](https://github.com/SauleBikentaevaPI330B/lab/blob/main/Снимок%20экрана%202025-12-20%20004416.png)
![1](https://github.com/SauleBikentaevaPI330B/lab/blob/main/Снимок%20экрана%202025-12-20%20004428.png)
![1](https://github.com/SauleBikentaevaPI330B/lab/blob/main/Снимок%20экрана%202025-12-20%20004434.png)
![1](https://github.com/SauleBikentaevaPI330B/lab/blob/main/Снимок%20экрана%202025-12-20%20004439.png)


Dockerfile:
```FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y openssh-server python3 && \
    mkdir /var/run/sshd && \
    useradd -m -s /bin/bash ansible && \
    echo "ansible:ansible" | chpasswd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config && \
    sed -i 's/UsePAM yes/UsePAM no/' /etc/ssh/sshd_config

RUN mkdir -p /home/ansible/.ssh

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]```


docker-compose.yml
```version: '3'
services:
  ansible-managed-host:
    build: .
    ports:
      - "2222:22"
    restart: unless-stopped```

task3_files.yml
```---
- name: Work with files
  hosts: managed_hosts
  tasks:
    - name: Create multiple directories
      file:
        path: /tmp/{{ item }}
        state: directory
        mode: '0755'
      loop:
        - test_dir1
        - test_dir2
        - test_dir3

    - name: Create files in directories
      copy:
        content: "This is {{ item }} file\n"
        dest: /tmp/{{ item }}/content.txt
      loop:
        - test_dir1
        - test_dir2
        - test_dir3

    - name: Display files
      command: cat /tmp/{{ item }}/content.txt
      loop:
        - test_dir1
        - test_dir2
        - test_dir3
      register: file_content

    - name: Show file contents
      debug:
        msg: "{{ item.stdout }}"
      loop: "{{ file_content.results }}"```
