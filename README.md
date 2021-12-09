# Use-Ansible-to-Install-and-Set-Up-Docker

## Running this playbook will perform the following actions on your Ansible hosts

```
1. Install aptitude, which is preferred by Ansible as an alternative to the apt package manager.
2. Install the required system packages.
3. Install the Docker GPG APT key.
4. Add the official Docker repository to the apt sources.
5. Install Docker.
6. Install the Python Docker module via pip.
7. Pull the centos image from Docker Hub.
8. Create the  containers using the image which we Pull from Docker Hub.
```



```

---
- name: Docker setup
  hosts: localhost
  become: true

  tasks:
     - name: Install prerequisites
       apt: name=aptitude update_cache=yes state=latest force_apt_get=yes

     - name: instal requir packages.
       apt: name={{ item }} update_cache=yes state=latest
       loop: [ 'apt-transport-https', 'ca-certificates', 'curl', 'software-properties-common', 'python3-pip', 'virtualenv', 'python3-setuptools']

     - name: add gpt key
       apt_key:
          url: https://download.docker.com/linux/ubuntu/gpg
          state: present

     - name: add docker repository to apt
       apt_repository:
          repo: deb https://download.docker.com/linux/ubuntu bionic stable
          state: present

     - name: install docker
       apt: name={{ item }} update_cache=yes state=latest
       loop: [ docker-ce, docker-ce-cli, containerd.io ]

     - name: start docker
       service:
          name: docker
          state: started
          enabled: yes

     - name: Install Docker Module for Python
       pip:
          name: docker

     - name: pull image
       docker_image:
          name: centos

     - name: create a docker container
       docker_container:
          name: centos-image
          image: centos
          state: started
          recreate: yes
          ports:
             - "8080:80"
          tty: true
          detach: true
          
          
          ```
