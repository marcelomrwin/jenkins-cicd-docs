### Montagem do container para performance-test

**Certifique-se de que o plugin docker-plugin está devidamente instalado**

- Acesse **Gerenciar Jenkins &rarr; Configurar o sistema**
- Navegue até a área de Nuvem (Cloud)

### Configurando uma imagem docker para testes de performance
- Acesse o host support-tools que provê o ambiente Docker. Na configuração parão o acesso se dá através do comando:
```
ssh admin@10.1.123.206
```
_obs: Lembre-se de alterar o IP caso tenha modificado a configuração padrão_

### Garanta que o usuário admin e jenkins possuam permissão para executar containers docker.
```
sudo usermod -aG docker admin
sudo usermod -aG docker jenkins
```

- Dentro do host faça o pull e execute a imagem base que será utilizada para executar testes de performance.
```
docker run -it ubuntu /bin/bash
```
- Verifique que a imagem base foi baixada no repositório local

### Configurando o container Docker para testes de performance

Dentro do container execute os comandos abaixo

```
adduser jenkins
apt-get update
apt-get install openssh-server -y
mkdir /var/run/sshd
apt-get update
apt install openjdk-8-jdk git subversion httping -y
cd /tmp
wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.16/bin/apache-tomcat-9.0.16.tar.gz
su jenkins
mkdir /home/jenkins/tomcat
tar xzvf apache-tomcat-9.0.16.tar.gz -C /home/jenkins/tomcat --strip-components=1
exit
chmod -R 777 /home/jenkins/tomcat

mkdir /home/jenkins/workspace
chown -R jenkins:jenkins /home/jenkins/workspace

mkdir /opt/jmeter
cd /tmp
wget https://archive.apache.org/dist/jmeter/binaries/apache-jmeter-3.3.tgz

tar xzvf apache-jmeter-3.3.tgz -C /opt/jmeter --strip-components=1
chown -R jenkins:jenkins /opt/jmeter/
chmod -R 777 /opt/jmeter/
```

### Instale o cliente curl
```
apt-get install curl -y
```

### Saia do container
```
exit
```

### Faça o commit do container
```
docker ps -a
```
![](/images/fig111.png)
```
docker commit <CONTAINER_ID> performance-test-agent-0.1
```
_Selecione o container mais recente representando o último container trabalhado, no exemplo acima **718eae83bf4f**_

### Configurando o template do Docker no Jenkins
- Retorne ao Jenkins e acesse **Gerenciar Jenkins &rarr; Configurar Sistema**
- Navegue até a área de **Nuvem &rarr; Docker**
- Clique  em **Docker Agent templates**
- Clique em **Add Docker Template**

No formulário preencha com os dados:
- **Labels:** docker-performance
- **Enabled:** Deixar marcado
- **Name:** docker-node-performance
- **Docker Image:** performance-test-agent-0.1
- **Remote File System Root:** /home/jenkins
- **Connect method:** Connect with SSH
  - **SSH key:** Use configured SSH credentials
    - **SSH Credentials:** Selecione _Jenkins LDAP_
    - **Host Key Verification Strategy:** Non verifying Verification Strategy
- **Pull strategy:** Never pull
- Clique em **Salvar**
![](/images/fig112.png)
