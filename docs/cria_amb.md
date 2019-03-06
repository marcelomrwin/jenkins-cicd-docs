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
  - 1Gb RAM
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
  - 1Gb de RAM
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

O node que será utilizado como _bastion_ (o host sob o qual o playbook ansible será executado) deve ter permissão de acesso livre a todos os outros nodes. Normalmente este passo é executado compartilhando a chave publica entre os nodes. O seguinte [tutorial](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos7) demonstra como proceder.
Também é importante garantir que todas as máquinas conheçam uma a outra. Este passo pode ser alcançado através da edição do arquivo /etc/hosts ou através da configuração de um DNS.

É obrigatório que todas as máquinas tenham permissão de acesso à internet para realizar downloads dos pacotes necessários.

Por fim deve-se garantir que todos os pacotes estejam devidamente atualizados. Isto é conseguido através do comando `sudo yum update -y`.

**Se os IPs sugeridos forem alterados é necessário que todos os arquivos de hosts e playbooks reflitam estas alterações.**

**Se os nomes sugeridos forem alterados é necessário que todos os arquivos de hosts e playbooks reflitam estas alterações.**

### Procedendo com a criação do ambiente

```
cd ansible
ansible-playbook -i hosts playbook.yml -vv
```
<i><b>observação:</b>
  - As máquinas configuradas em [hosts](ansible/hosts) devem estar devidamente configuradas
</i>



O ansible configurará as VMs e também instalará os pacotes necessários para o funcionamento do jenkins bem como as ferramentas:
- JDK 1.8
- Maven 3.5.4
