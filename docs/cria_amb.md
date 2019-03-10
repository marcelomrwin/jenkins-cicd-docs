### Criar o ambiente com Vagrant {docsify-ignore}

<i>Os comandos abaixo fazem referência a um terminal unix</i>

```
cd vagrant
```
### 1. Criar as máquinas virtuais no VirtualBox

```
vagrant up
```
### 1.1 Se desejar destruir as máquinas utilizar
```
vagrant destroy -f
```

### 2. Após executar o `vagrant up` execute o playbook ansible

O comando `vagrant up` criará um ambiente conforme segue:
- VM <b>jenkins-master</b>
  - Centos 7
  - 2 CPUs
  - 1Gb RAM
  - 2 NICs
    - Local Network 192.168.56.110
    - NAT
- VM <b>jenkins-slave1</b>
  - Centos 7
  - 2 CPUs
  - 1Gb RAM
  - 2 NICs
    - Local Network 192.168.56.120
    - NAT
- VM <b>jenkins-slave2</b>
  - Centos 7
  - 2 CPUs
  - 1Gb RAM
  - 2 NICs
    - Local Network 192.168.56.130
    - NAT
- VM <b>support-tools</b>
  - Centos 7
  - 2 CPUs
  - 2Gb RAM
  - 2 NICs
    - Local Network 192.168.56.140
    - NAT
- VM <b>nexus</b>
  - Centos 7
  - 2 CPUs
  - 2Gb RAM
  - 2 NICs
    - Local Network 192.168.56.150
    - NAT
- VM <b>sonarqube</b>
  - Centos 7
  - 2 CPUs
  - 3Gb RAM
  - 2 NICs
    - Local Network 192.168.56.160
    - NAT

#### 2.1 Instalação do VirtualBox Guest Additions
Caso deseje instalar o plugin do virtualbox execute o comando
```
vagrant plugin install vagrant-vbguest
```

### Criar o ambiente sem Vagrant

Caso deseje provisionar o ambiente sem o uso do vagrant a seguinte infraestrutura deve ser provisionada.

- 1 Host CentOS 7
  - 1Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 192.168.56.110 (O IP pode ser alterado)
  - Nomeada como jenkins-master (O nome pode ser alterado)

- 1 Host CentOS 7
  - 1Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 192.168.56.120 (O IP pode ser alterado)
  - Nomeada como jenkins-slave1 (O nome pode ser alterado)

- 1 Host CentOS 7
  - 1Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 192.168.56.130 (O IP pode ser alterado)
  - Nomeada como jenkins-slave2 (O nome pode ser alterado)

- 1 Host CentOS 7
  - 2Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 192.168.56.140 (O IP pode ser alterado)
  - Nomeada como support-tools (O nome pode ser alterado)

- 1 Host CentOS 7
  - 2Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 192.168.56.150 (O IP pode ser alterado)
  - Nomeada como nexus (O nome pode ser alterado)

- 1 Host CentOS 7
  - 4Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 192.168.56.160 (O IP pode ser alterado)
  - Nomeada como sonarqube (O nome pode ser alterado)

#### Detalhes do ambiente
O host que será utilizado como _bastion_ (o host sob o qual o playbook ansible será executado) deve ter permissão de acesso livre a todos os outros nodes. Normalmente este passo é executado compartilhando a chave publica entre os nodes. O seguinte [tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos7) demonstra como proceder.

#### Compartilhando chave do usuário local com as máquinas remotas.
- Edite o arquivo hosts-root. Para fins de teste existem dois arquivos mas para execução escolher um deles.
  - hosts-root (Criado para testes em ambiente VirtualBox)

  ```
  [vars]
  ansible_ssh_user=root
  ansible_become=yes

  [nodes]
  jenkins-master ansible_host=192.168.56.110
  jenkins-slave1 ansible_host=192.168.56.120
  jenkins-slave2 ansible_host=192.168.56.130
  support-tools ansible_host=192.168.56.140
  nexus ansible_host=192.168.56.150
  sonarqube ansible_host=192.168.56.160

  [master]
  jenkins-master ansible_host=192.168.56.110

  [slaves]
  jenkins-slave1 ansible_host=192.168.56.120
  jenkins-slave2 ansible_host=192.168.56.130

  [tools]
  support-tools ansible_host=192.168.56.140

  [repository]
  nexus ansible_host=192.168.56.150

  [sonar]
  sonarqube ansible_host=192.168.56.160
  ```

  - hosts-root-vmware (Criado para testes em ambiente vmware)

  ```
  [vars]
  ansible_ssh_user=root
  ansible_become=yes

  [nodes]
  jenkins-master ansible_host=10.1.124.128
  jenkins-node1 ansible_host=10.1.124.129
  jenkins-node2 ansible_host=10.1.124.130
  support-tools ansible_host=10.1.124.131
  nexus ansible_host=10.1.124.132
  sonarqube ansible_host=10.1.124.133

  [master]
  jenkins-master ansible_host=10.1.124.128

  [nodes]
  jenkins-node1 ansible_host=10.1.124.129
  jenkins-node2 ansible_host=10.1.124.130

  [tools]
  support-tools ansible_host=10.1.124.131

  [repository]
  nexus ansible_host=10.1.124.132

  [sonar]
  sonarqube ansible_host=10.1.124.133
  ```

O seguinte playbook executa a tarefa de transferir a chave local para o servidor.
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

```
cd ansible
ansible-playbook playbook-copy-id.yml -vv
```
**Ficar atento ao password do usuário root do host, o ansible solicitará a digitação para cada host que ele configurar.**

Também é importante garantir que todas as máquinas conheçam uma a outra. Este passo pode ser alcançado através da edição do arquivo /etc/hosts ou através da configuração de um DNS.

É obrigatório que todas as máquinas tenham permissão de acesso à internet para realizar downloads dos pacotes necessários.

Por fim deve-se garantir que todos os pacotes estejam devidamente atualizados. Isto é conseguido através do comando `sudo yum update -y`.

**Se os IPs sugeridos forem alterados é necessário que todos os arquivos de hosts e playbooks reflitam estas alterações.**

**Se os nomes sugeridos forem alterados é necessário que todos os arquivos de hosts e playbooks reflitam estas alterações.**

Com as máquinas provisionadas executar primeiro o playbook ansible/playbook-root.yml utilizando como arquivo de inventário o ansible/hosts-root ou hosts-root-vmware.

**Antes de executar o playbook observe o arquivo de variáveis playbook-vars.yml**
```
---
default_domain: cicd.local
jenkins_master_name: jenkins-master
jenkins_master_ip: 10.1.124.128
jenkins_node1_name: jenkins-node1
jenkins_node1_ip: 10.1.124.129
jenkins_node2_name: jenkins-node2
jenkins_node2_ip: 10.1.124.130
tools_name: support-tools
tools_ip: 10.1.124.131
nexus_name: nexus
nexus_ip: 10.1.124.132
sonar_name: sonarqube
sonar_ip: 10.1.124.133
```
**Ajuste o arquivo de acordo com a sua infraestrutura.**

Após ajustar o arquivo de variáveis e inventário executar o playbook conforme abaixo:
```
ansible-playbook -i hosts-root-vmware playbook-root.yml -vv
```
OU
```
ansible-playbook -i hosts-root playbook-root.yml -vv
```

### Procedendo com a criação do ambiente

**Antes de executar o playbook abaixo verifique o certificado nexus/ca.crt. Este certificado foi gerado para o ip 10.1.124.132, caso possua outro certificado ou tenha alterado o ip este certificado deve ser gerado novamente**

```
cd ansible
ansible-playbook -i hosts-vmware playbook.yml -vv
```
<i><b>observação:</b>
  - As máquinas configuradas em [hosts](ansible/hosts) devem estar devidamente configuradas
</i>



O ansible configurará as VMs e também instalará os pacotes necessários para o funcionamento do jenkins bem como as ferramentas:
- JDK 1.8
- Maven 3.5.4
