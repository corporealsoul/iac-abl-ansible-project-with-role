### Inventory / Hosts

    [anup@rhel-92-104 playbooks]$ sudo nano /etc/ansible/hosts 
    
    [controller]
    192.168.56.104  ansible_ssh_user=anup   ansible_ssh_pass=' ' ansible_become_pass=' ' # Host address, user_name and password
    
    [worker]
    192.168.56.108  ansible_ssh_user=anup   ansible_ssh_pass=' ' ansible_become_pass=' ' # Host address, user_name and password


### Install Docker, On Ubuntu

`[anup@rhel-92-104 ~]$ cd /etc/ansible/roles/`

`[anup@rhel-92-104 roles]$ sudo ansible-galaxy init install_configure_docker`

`[anup@rhel-92-104 roles]$ ls -ltr`

`[anup@rhel-92-104 roles]$ cd install_configure_docker/`

`[anup@rhel-92-104 install_configure_docker]$ sudo nano ./tasks/main.yml `

    ---
    # tasks file for install_configure_docker
    - name: Update apt cache
      apt:
        update_cache: yes
      become: yes
    
    - name: Install required packages for Docker
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
        state: present
      become: yes
    
    - name: Add Docker GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present
      become: yes
    
    - name: Add Docker repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable
      become: yes
    
    - name: Install Docker
      apt:
        name: docker-ce
        state: present
      become: yes
    
    - name: Start and enable Docker service
      service:
        name: docker
        state: started
        enabled: yes
      become: yes

<br>

### Install NGNIX, On Ubuntu

`[anup@rhel-92-104 ~]$ cd /etc/ansible/roles/`

`[anup@rhel-92-104 roles]$ sudo ansible-galaxy init install_configure_nginx`

`[anup@rhel-92-104 roles]$ ls -ltr`

`[anup@rhel-92-104 roles]$ cd install_configure_nginx/`

`[anup@rhel-92-104 install_configure_nginx]$ sudo nano ./tasks/main.yml`

    ---
    - name: Update apt cache
      apt:
        update_cache: yes
      become: yes
    
    - name: Install NGINX
      apt:
        name: nginx
        state: present
      become: yes
      notify:
        - Restart NGINX

`[anup@rhel-92-104 ~]$ sudo nano /etc/ansible/roles/install_configure_nginx/handlers/main.yml `

    ---
    - name: Restart NGINX
      service:
        name: nginx
        state: restarted
      become: yes

<br>

### Install Apache, On Ubuntu

`[anup@rhel-92-104 ~]$ cd /etc/ansible/roles/`

`[anup@rhel-92-104 roles]$ sudo ansible-galaxy init install_configure_apache`

`[anup@rhel-92-104 roles]$ cd install_configure_apache/`

`[anup@rhel-92-104 install_configure_apache]$ cd tasks/`

`[anup@rhel-92-104 tasks]$ sudo nano main.yml `

    ---
    - name: Update apt cache
      apt:
        update_cache: yes
      become: yes
    
    - name: Install Apache2
      apt:
        name: apache2
        state: present
      become: yes
    
    - name: Configure Apache2 to listen on port 81
      lineinfile:
        path: /etc/apache2/ports.conf
        regexp: '^Listen 80$'
        line: 'Listen 81'
        backup: yes
      notify:
        - Restart Apache2
      become: yes

`[anup@rhel-92-104 ~]$ sudo nano /etc/ansible/roles/install_configure_apache/handlers/main.yml `

    ---
    # handlers file for install_configure_apache
    - name: Restart Apache2
      service:
        name: apache2
        state: restarted
      become: yes

<br>

### Install Tomcat, On Ubuntu

`[anup@rhel-92-104 ~]$ cd /etc/ansible/roles/`

`[anup@rhel-92-104 roles]$ sudo ansible-galaxy init install_configure_tomcat`

`[anup@rhel-92-104 roles]$ ls -ltr`

`[anup@rhel-92-104 roles]$ cd install_configure_tomcat/`

`[anup@rhel-92-104 install_configure_tomcat]$ sudo nano ./tasks/main.yml`

    ---
    - name: Update apt cache
      apt:
        update_cache: yes
      become: yes
    
    - name: Install Java
      apt:
        name: openjdk-8-jdk
        state: present
      become: yes
    
    - name: Download Tomcat tarball
      get_url:
        url: "https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.79/bin/apache-tomcat-9.0.79.tar.gz"
        dest: /tmp/apache-tomcat.tar.gz
      become: yes
    
    - name: Extract Tomcat
      unarchive:
        src: /tmp/apache-tomcat.tar.gz
        dest: /opt
        remote_src: yes
      become: yes
    
    - name: Set permissions
      file:
        path: /opt/apache-tomcat-9.0.79
        state: directory
        recurse: yes
        owner: anup
        group: anup
      become: yes
    
    - name: Start Tomcat
      command: /opt/apache-tomcat-9.0.79/bin/startup.sh
      become: yes
      notify: Start Tomcat

`[anup@rhel-92-104 ~]$ sudo nano /etc/ansible/roles/install_configure_tomcat/handlers/main.yml `

    ---
    # handlers file for install_configure_tomcat
    
    - name: Start Tomcat
      command: /opt/apache-tomcat-9.0.79/bin/startup.sh
      become: yes
      listen: "Start Tomcat"

<br>

### Install Postgres, On Ubuntu

`[anup@rhel-92-104 ~]$ cd /etc/ansible/roles/`

`[anup@rhel-92-104 roles]$ sudo ansible-galaxy init install_configure_postgres`

`[anup@rhel-92-104 roles]$ ls -ltr`

`[anup@rhel-92-104 roles]$ cd install_configure_postgres/`

`[anup@rhel-92-104 install_configure_postgres]$ sudo nano ./tasks/main.yml`

    ---
    - name: Update apt cache
      apt:
        update_cache: yes
      notify: Restart PostgreSQL
    
    - name: Install PostgreSQL packages
      apt:
        name:
          - postgresql
          - postgresql-contrib
        state: present

`[anup@rhel-92-104 ~]$ sudo nano /etc/ansible/roles/install_configure_postgres/handlers/main.yml `

    ---
    # handlers file for install_configure_postgres
    
    - name: Restart PostgreSQL
      systemd:
        name: postgresql
        state: restarted
        enabled: yes
