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
  - 4Gb RAM
  - 2 NICs
    - Local Network 192.168.56.160
    - NAT

#### 2.1 Instalação do VirtualBox Guest Additions
Caso deseje instalar o plugin do virtualbox execute o comando
```
vagrant plugin install vagrant-vbguest
```
