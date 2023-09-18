# Highly Available PostgreSQL 13
### Deployment: quick start
0. [Install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) on one control node (which could easily be a laptop)

```
sudo apt update && sudo apt install -y python3-pip sshpass git
pip3 install ansible
```

1. Download or clone this repository

```
git clone https://gitlab.sch.ocrv.com.rzd/blockchain/trainees/postgresha-svc.git
```

2. Go to the playbook directory

```
cd  postgresha-svc/
```
3. Edit the inventory file

##### Specify (non-public) IP addresses and connection settings (`ansible_user`, `ansible_ssh_pass` for your environment

```
nano hosts
```
##### Minimum set of variables: 
- `priority` # for keepalived 
- `cluster_vip` # for client access to databases in the cluster 
- `interface` # interface of each node (like a ``eth0``)
- `ansible_user` 
- `ansible_ssh_pass`
- `ansible_sudo_pass`
- hostnames and addresses for each node

4. Edit the env [.env](./.env)

```
nano .env
```
##### Minimum set of variables: 
- `rep_user`: replica user
- `rep_passwd`: replica passwd
- `postgres_user`: postgres user
- `postgres_passwd`: postgres passwd
- duplicate hostnames and addresses from hosts

5. Try to connect to hosts

```
ansible all -m ping
```

6. Run playbook:

```
ansible-playbook deploy.yml
```
7. Если уже был проинциализирован кластер до этого вы заново раскатываете кластер, то необходимо вручную зайти на каждую ноду и прописать перед установкой нового кластера такую команду (для этого я предусмотрел таймаут в 5 минут после установки etcd)

```
patronictl remove <cluster_name>
```

### Available tags
    prepare # for  add hosts to /etc/hosts and remove all trash

- for install and run services

    ```
    etcd 
    patroni
    pgbouncer
    keepalived
    haproxy
    ```



### Когда всё подняли, то как оно работает 
по порту адресу vip:5432 можно подключиться к мастеру и выполнять любые транзакции
А по порту 5001 подлкючение идет на ReadOnly реплики.

Также рекомендую отключать pg bouncer 