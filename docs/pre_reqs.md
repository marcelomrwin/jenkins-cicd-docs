### Pré Requisitos {docsify-ignore}

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads) a partir da versão 5.2.26
- [Vagrant](https://www.vagrantup.com/downloads.html) a partir da versão 2.2.3
- [Ansible](https://www.ansible.com/resources/get-started) a partir da versão 2.7.6
- [Git](https://git-scm.com/downloads) a partir da versão 2.20.1

### Geração de Chave SSH (Caso não possua uma chave id_rsa)
```
ssh-keygen -q -t rsa -b 4096
```
Não inclua senha/password para proteger a chave.
Deixe a configuração padrão ~/.ssh/id_rsa.

O seguinte playbook executa a tarefa de transferir a chave local para o servidor. O playbook está localizado na pasta ansible/playbook-copy-id.yml
```
---
- name: copy local ssh to remote hosts
  hosts: 127.0.0.1
  connection: local
  tasks:
    - name: copy ssh key
      command: ssh-copy-id root@{{ item }}
      with_items:
        - 10.1.124.128
        - 10.1.124.129
        - 10.1.124.130
        - 10.1.124.131
        - 10.1.124.132
        - 10.1.124.133
```

Modifique estes arquivos para que reflitam exatamente os IPs e os nomes dos hosts que receberão as configurações.
Execute o seguinte playbook para transferir a chave local para o ambiente remoto

#### Detalhes do ambiente
O host que será utilizado como _bastion_ (o host sob o qual o playbook ansible será executado) deve ter permissão de acesso livre a todos os outros nodes. Normalmente este passo é executado compartilhando a chave publica entre os nodes. O seguinte [tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos7) demonstra como proceder.

### Execute o playbook para transferir as chaves do usuário root
```
cd ansible
ansible-playbook playbook-copy-id.yml -vv
```

**Ficar atento ao password do usuário root do host, o ansible solicitará a digitação para cada host que ele configurar.**
Digite a senha do usuário root sempre que solicitado.

### Execute o playbook para criação do usuário admin
```
cd ansible\admin
ansible-playbook -i hosts-vmware playbook-user-admin.yml -vv
```
