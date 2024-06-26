Первые шаги с Ansible
1. Прикрепил два Vagrantfile (Vagrantfile_nginx1 и Vagrantfile_nginx)
2. Разворачиваем сначала Vagrantfile_nginx, затем Vagrantfile
* Разворачиваются две виртуалки nginx и nginx1 с ip адресами 192.168.11.150 и 192.168.11.151, на nginx1 генерируется публичный ssh ключ и 
* отправляется на машину nginx с целью авторизации без логина и пароля в дальнейшем;

3. В Ubuntu, созданной Vagrantfile_nginx (nginx) редактируем файлы:
- ~/Otus/nginx.yml
- ~/Otus/inventory
- ~/Otus/ansible.cfg
- ~/Otus/templates/nginx.conf.j2

4. Через редактор nano добавляем в файл nginx.yml следующее:
---
- name: NGINX | Install and configure NGINX
  hosts: nginx1
  become: true
  vars:
    nginx_listen_port: 8080

  tasks:
    - name: update
      apt:
        update_cache=yes
      tags:
        - update apt

    - name: NGINX | Install NGINX
      apt:
        name: nginx
        state: latest
      notify:
        - restart nginx
      tags:
        - nginx-package

    - name: NGINX | Create NGINX config file from template
      template:
        src: /templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify:
        - reload nginx
      tags:
        - nginx-configuration

  handlers:
    - name: restart nginx
      systemd:
        name: nginx
        state: restarted
        enabled: yes

    - name: reload nginx
      service:
        name: nginx
        state: reloaded



5. через редактор nano в файл inventory добавляем следующее:
nginx1 ansible_host=192.168.11.151 ansible_port=22 ansible_private_key=~/.ssh/config



6. Через редактор nano в файл ansible.cfg добавляем следующее:
[defaults]
inventory = Otus/
remote_user = vagrant
host_key_checking = False
retry_files_enabled = False



7. Через редактор nano добавляем в файл ~/Otus/templates/nginx.conf.j2 следующее:
# {{ ansible_managed }}
events {
    worker_connections 1024;
}

http {
    server {
        listen       {{ nginx_listen_port }} default_server;
        server_name  default_server;
        root         /usr/share/nginx/html;

        location / {
        }
    }
}





8. после ввода команды ansible-playbook nginx.yml  -i inventory получим следующий вывод:

vagrant@nginx:~/Otus$ ansible-playbook nginx.yml  -i inventory

PLAY [NGINX | Install and configure NGINX] **************************************

TASK [Gathering Facts] **********************************************************
ok: [nginx1]

TASK [update] *******************************************************************
changed: [nginx1]

TASK [NGINX | Install NGINX] ****************************************************
ok: [nginx1]

TASK [NGINX | Create NGINX config file from template] ***************************
ok: [nginx1]

PLAY RECAP **********************************************************************
nginx1                     : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

vagrant@nginx:~/Otus$
vagrant@nginx:~/Otus$
vagrant@nginx:~/Otus$
vagrant@nginx:~/Otus$
vagrant@nginx:~/Otus$ curl http://192.168.11.151:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

9. Готово, все работает)))
