### Criar o ambiente {docsify-ignore}

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

```
ansible-playbook -i ../ansible/hosts ../ansible/playbook.yml -vv
```
<i><b>observação:</b>
  - o comando acima considera sua execução dentro da pasta vagrant
  - As máquinas configuradas em [hosts](ansible/hosts) devem estar devidamente configuradas
</i>

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

O ansible configurará as VMs e também instalará os pacotes necessários para o funcionamento do jenkins bem como as ferramentas:
- JDK 1.8
- Maven 3.5.4
