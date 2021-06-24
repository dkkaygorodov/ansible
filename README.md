# ansible
Напишите плейбук Ansible, который делает следующее:

- ставит nginx, zsh, wget
- клонирует из git в /var/www/ очень простую web страничку "Under construction"
- кладет новую конфигурацию nginx которая направляет на эту страничку
- настраивает в sysctl параметр fs.files-max в 1204000 и somaxconn в 65535
- прописывает на этой машинке 2 разных ssh ключа в /root

Целевая ОС - ubuntu 18.04LTS Код будет доступен команде, которая им же будет производить изменения в проекте (типовые вещи - управление ключами ssh, изменения конфига nginx, добавление новых пакетов и т.п.)

Результат разместите в приватном репозитории на Github.

Заболел короной, остается только "причесать" - а именно: разделить установку nginx на несколько задач в стиле (и потом протестить):

    - name: Скачиваем PGP-key
      get_url:
        url: http://nginx.org/keys/nginx_signing.key
        dest: /etc/nginx_signing.key

    - name: Устанавливаем PGP-key
      apt_key:
        file: /etc/nginx_signing.key
        state: present

    - name: Добавляем репозиторий Nginx deb
      ansible.builtin.apt_repository:
        repo: deb https://nginx.org/packages/mainline/ubuntu/ bionic nginx
        update_cache: yes
        state: present

    - name: Устанавливаем Nginx
      apt: 
        name: nginx
        update_cache: yes
        state: latest
