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


