### Acessar a API remota do Docker
A API remota do docker está acessível através da URL http://10.1.123.206:4243

Um exemplo de como obter as imagens no repositório remoto:
```
curl -X GET http://10.1.123.206:4243/images/json
```

### Configurando o Jenkins para acessar a API do Docker

**Certifique-se de que o plugin docker-plugin está devidamente instalado**

- Acesse **Gerenciar Jenkins &rarr; Configurar o sistema**
- Navegue até a área de Nuvem (Cloud)

![](/images/fig67-docker.png)
- Clique em **Adicionar uma nova nuvem &rarr; Docker**
- Clique em **Docker Cloud details...**

Preencha o formulário com as informações
![](/images/fig68-docker.png)
  - **Name:** _Default Docker Host_
  - **Docker Host URI:** _tcp://10.1.123.206:4243/_
  - utilize o botão **Test Connection** para garantir que o jenkins consegue comunicação com o servidor Docker
  - **Enabled:** Deixar marcado
  - **Expose DOCKER_HOST:** Deixar marcado
  - Clique em **Salvar**

### Configurando uma imagem docker padrão
- Acesse o host support-tools que provê o ambiente Docker. Na configuração parão o acesso se dá através do comando:
```
ssh root@10.1.123.206
```
_obs: Lembre-se de alterar o IP caso tenha modificado a configuração padrão_

- Dentro do host faça o pull e execute a imagem base que será utilizada para gerar artefatos java.
```
docker run -it ubuntu /bin/bash
```
- Verifique a imagem base foi baixada no repositório local

- Crie o usuário jenkins
```
adduser jenkins
```
- Informe os dados solicitados até a conclusão da criação do usuário.

- Certifique-se de que consegue usar o usuário jenkins
```
su jenkins
```
- Volte para o usuário root
```
exit
```


- Instale alguns pacotes essenciais
```
apt-get update
apt -y install vim
apt-get install openssh-server -y
mkdir /var/run/sshd
```
- Instale o cliente subversion
```
apt-get install subversion -y
```
- Instale o cliente git
```
apt-get install git -y
```
- Instale a java sdk 8
```
apt-get install openjdk-8-jdk -y
```
- Instale o Maven
```
apt-get install maven -y
```
- Configure o Maven
```
cd /opt
wget http://www-us.apache.org/dist/maven/maven-3/3.5.4/binaries/apache-maven-3.5.4-bin.tar.gz
tar -xf apache-maven-3.5.4-bin.tar.gz
chown -R jenkins:jenkins /opt/apache-maven-3.5.4/
chmod -R 777 /opt/apache-maven-3.5.4/
rm -rf apache-maven-3.5.4-bin.tar.gz
```

### **O conteúdo do certificado abaixo corresponde ao arquivo ansible/nexus/ca.csr. Caso possua outro certificado ou tenha gerado um novo atualize o conteúdo do arquivo**</br> {docsify-ignore}
- Crie o arquivo de certificado do nexus na imagem docker
  - digite `vi ca.crt`
  - na janela do vi que abriu digite `A`
  - cole o seguinte conteúdo dentro do arquivo:  
  ```
  -----BEGIN CERTIFICATE-----
  MIICjjCCAfcCFE4ESn3CfwxCjCtQLuc/T2TcxKARMA0GCSqGSIb3DQEBCwUAMIGF
  MQswCQYDVQQGEwJicjEXMBUGA1UECAwOcmlvIGRlIGphbmVpcm8xFzAVBgNVBAcM
  DnJpbyBkZSBqYW5laXJvMQ8wDQYDVQQKDAZhbmNpbmUxFTATBgNVBAMMDDEwLjEu
  MTIzLjIwNzEcMBoGCSqGSIb3DQEJARYNYW5jaW5lQGdvdi5icjAeFw0xOTA0MTYw
  MTE3NDlaFw0yMDA0MTUwMTE3NDlaMIGFMQswCQYDVQQGEwJicjEXMBUGA1UECAwO
  cmlvIGRlIGphbmVpcm8xFzAVBgNVBAcMDnJpbyBkZSBqYW5laXJvMQ8wDQYDVQQK
  DAZhbmNpbmUxFTATBgNVBAMMDDEwLjEuMTIzLjIwNzEcMBoGCSqGSIb3DQEJARYN
  YW5jaW5lQGdvdi5icjCBnzANBgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEAyPXtDchW
  JLz65fug2QsZ5YWeErARa35Z0EQPvjl5yIKYUgtuLnWafH+IHt8ZtqcZfX4Wr5sT
  QdLq4YcoFh1edjRseOkGj1WtNwe2gjTVv+msjMCLFiAeMXtqvzPtNvaxjNLFt2ED
  0V2xfoo3QwwG4PklGdYwJLzsPr7fVmAaZ5kCAwEAATANBgkqhkiG9w0BAQsFAAOB
  gQB62vWVrx7eGSYV2au9/bmPQxWIghnUeRjuA1+0WHIkIruOXYp5aSlDaTDRG5kg
  Y4zch1QuQ44ROuA/B6POgQMCX47w4Q+72NCKkxOAKi3LouUTYVZoXTJ83+edy3eh
  z9kjIsgwaMRDYgXWZ3E1z8kOirZQHcMK8cySq5QqzI+tgQ==
  -----END CERTIFICATE-----
  ```
  - pressione `esc`
  - digite `:`
  - digite `x`
  - pressione `enter`

- importe o arquivo ca.crt para o arquivo de segurança do java
```
keytool -import -storepass changeit -noprompt -alias nexus -keystore /usr/lib/jvm/java-8-openjdk-amd64/jre/lib/security/cacerts -trustcacerts -file ca.crt
```
- saia do container docker digitando `exit`
- faça o commit da imagem customizada.
  - digite `docker ps -a`
  ```
  [root@support-tools ~]# docker ps -a
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
  a854c73fe8af        ubuntu     "/bin/bash"   2 hours ago         Exited (0) 8 seconds ago                       maven
  ```
  - observe o valor do _CONTAINER ID_, utilizaremos este valor no comando abaixo.
  - execute o comando:
  ```
  docker commit a854c73fe8af maven-build-node-0.1
  ```
- observe a imagem criada
```
[root@support-tools ~]# docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
maven-build-node-0.1   latest              33aa82fe6286        11 seconds ago      825MB
maven                  ubuntu           985f3637ded4        4 months ago        635MB
```

### Configurando o template do Docker no Jenkins
- Retorne ao Jenkins e acesse **Gerenciar Jenkins &rarr; Configurar Sistema**
- Navegue até a área de **Nuvem &rarr; Docker**
- Clique  em **Docker Agent templates**

No formulário preencha com os dados:
- **Labels:** docker-build
- **Enabled:** Deixar marcado
- **Name:** docker-node-build
- **Docker Image:** maven-build-node-0.1
- **Remote File System Root:** /home/jenkins
- **Connect method:** Connect with SSH
  - **SSH key:** Use configured SSH credentials
    - **SSH Credentials:** Selecione _Jenkins LDAP_
    - **Host Key Verification Strategy:** Non verifying Verification Strategy
- **Pull strategy:** Never pull
- Clique em **Salvar**
![](/images/fig106.png)
