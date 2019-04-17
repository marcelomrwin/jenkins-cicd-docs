### Criar o ambiente {docsify-ignore}

A seguinte infraestrutura deve ser provisionada.

- 1 Host CentOS 7
  - 1Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 10.1.123.208 (O IP pode ser alterado)
  - Nomeada como rj-jenkins-master (O nome pode ser alterado)

- 1 Host CentOS 7
  - 1Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 10.1.123.209 (O IP pode ser alterado)
  - Nomeada como rj-jenkins-node1 (O nome pode ser alterado)

- 1 Host CentOS 7
  - 1Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 10.1.123.210 (O IP pode ser alterado)
  - Nomeada como rj-jenkins-node2 (O nome pode ser alterado)

- 1 Host CentOS 7
  - 2Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 10.1.123.206 (O IP pode ser alterado)
  - Nomeada como rj-support-tools (O nome pode ser alterado)

- 1 Host CentOS 7
  - 2Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 10.1.123.207 (O IP pode ser alterado)
  - Nomeada como rj-nexus (O nome pode ser alterado)

- 1 Host CentOS 7
  - 4Gb de RAM
  - 40G de Disco
  - 2 vCPUs
  - 1 Interface de Rede IP 10.1.123.189 (O IP pode ser alterado)
  - Nomeada como rj-sonarqube (O nome pode ser alterado)

#### Compartilhando chave do usuário local com as máquinas remotas.
- Edite o arquivo hosts-root
  - hosts-root-vmware (Criado para testes em ambiente vmware)

  ```
  [vars]
  ansible_ssh_user=root
  ansible_become=yes

  [nodes]
  rj-jenkins-master ansible_host=10.1.123.208
  rj-jenkins-node1 ansible_host=10.1.123.209
  rj-jenkins-node2 ansible_host=10.1.123.210
  rj-support-tools ansible_host=10.1.123.206
  rj-nexus ansible_host=10.1.123.207
  rj-sonarqube ansible_host=10.1.123.189

  [master]
  rj-jenkins-master ansible_host=10.1.123.208

  [nodes]
  rj-jenkins-node1 ansible_host=10.1.123.209
  rj-jenkins-node2 ansible_host=10.1.123.210

  [tools]
  rj-support-tools ansible_host=10.1.123.206

  [repository]
  rj-nexus ansible_host=10.1.123.207

  [sonar]
  rj-sonarqube ansible_host=10.1.123.189
  ```

Modifique este arquivo para que reflitam exatamente os IPs e os nomes dos hosts que receberão as configurações.
Execute o seguinte playbook para transferir a chave local para o ambiente remoto

Também é importante garantir que todas as máquinas conheçam uma a outra. Este passo pode ser alcançado através da edição do arquivo /etc/hosts ou através da configuração de um DNS.

É obrigatório que todas as máquinas tenham permissão de acesso à internet para realizar downloads dos pacotes necessários.

Por fim deve-se garantir que todos os pacotes estejam devidamente atualizados. Isto é conseguido através do comando `sudo yum update -y`.

**Se os IPs sugeridos forem alterados é necessário que todos os arquivos de hosts e playbooks-vars reflitam estas alterações.**

**Se os nomes sugeridos forem alterados é necessário que todos os arquivos de hosts e playbooks-vars reflitam estas alterações.**

Com as máquinas provisionadas executar primeiro o playbook *ansible/playbook-root.yml* utilizando como arquivo de inventário o  *hosts-root-vmware*.

**Antes de executar o playbook observe o arquivo de variáveis playbook-vars.yml**
```
---
default_domain: cicd.local
jenkins_master_name: rj-jenkins-master
jenkins_master_ip: 10.1.123.208
jenkins_node1_name: rj-jenkins-node1
jenkins_node1_ip: 10.1.123.209
jenkins_node2_name: rj-jenkins-node2
jenkins_node2_ip: 10.1.123.210
tools_name: rj-support-tools
tools_ip: 10.1.123.206
nexus_name: rj-nexus
nexus_ip: 10.1.123.207
sonar_name: rj-sonarqube
sonar_ip: 10.1.123.189
```
**Ajuste o arquivo de acordo com a sua infraestrutura.**

Após ajustar o arquivo de variáveis e inventário executar o playbook conforme abaixo:
```
cd ansible
ansible-playbook -i hosts-root-vmware playbook-root.yml -vv
```

### Checkpoint. {docsify-ignore}
Caso o recurso de snapshot esteja disponível este seria um bom ponto para gerar um das máquinas conforme configuração acima. Até este momento os playbooks apenas prepararam a base para a instalação dos demais recursos. Utilize um identificador como BASE no caso de realizar uma nova instalação partindo deste ponto.

<hr/>

### Procedendo com a criação do ambiente

**Antes de executar o playbook abaixo verifique o certificado nexus/ca.crt. Este certificado foi gerado para o ip 10.1.123.207, caso possua outro certificado ou tenha alterado o ip este certificado deve ser gerado novamente**

```
cd ansible
ansible-playbook -i hosts-vmware playbook.yml -vv
```
<i><b>observação:</b>
  - As máquinas configuradas em hosts-vmware devem estar devidamente configuradas
</i>

O ansible configurará as VMs e também instalará os pacotes necessários para o funcionamento do jenkins bem como as ferramentas:
- JDK 1.8
- Maven 3.5.4

### Solução de problemas (apenas se algum erro ocorreu no passo acima)
Caso durante a execução do playbook uma janela similar abaixo aparecer:
![](/images/fig76.png)
Force um novo reinício do servidor Jenkins (**Apenas se houve problemas conforme acima**)
```
ssh root@10.1.123.128 systemctl restart jenkins
```
