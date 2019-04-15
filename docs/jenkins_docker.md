### Acessar a API remota do Docker
A API remota do docker está acessível através da URL http://10.1.124.131:4243

Um exemplo de como obter as imagens no repositório remoto:
```
curl -X GET http://10.1.124.131:4243/images/json
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
  - **Docker Host URI:** _tcp://10.1.124.131:4243/_
  - utilize o botão **Test Connection** para garantir que o jenkins consegue comunicação com o servidor Docker
  - **Enabled:** Deixar marcado
  - **Expose DOCKER_HOST:** Deixar marcado
  - Clique em **Salvar**

### Configurando uma imagem docker padrão
- Acesse o host support-tools que provê o ambiente Docker. Na configuração parão o acesso se dá através do comando:
```
ssh root@10.1.124.131
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

- Crie o arquivo de certificado do nexus na imagem docker
  - digite `vi ca.crt`
  - na janela do vi que abriu digite `A`
  - cole o seguinte conteúdo dentro do arquivo:
  ```
  -----BEGIN CERTIFICATE-----
  MIICrDCCAhUCFHbqXaa7OU9KIEoYKmI2BX+zkke8MA0GCSqGSIb3DQEBCwUAMIGU
  MQswCQYDVQQGEwJCUjEXMBUGA1UECAwOUmlvIGRlIEphbmVpcm8xFzAVBgNVBAcM
  DlJpbyBkZSBKYW5laXJvMQswCQYDVQQKDAJUSTELMAkGA1UECwwCVEkxFTATBgNV
  BAMMDDEwLjEuMTI0LjEzMjEiMCAGCSqGSIb3DQEJARYTbmV4dXNAYW5jaW5lLmdv
  di5icjAeFw0xOTAzMDkxNTU4NTFaFw0yMTAyMjYxNTU4NTFaMIGUMQswCQYDVQQG
  EwJCUjEXMBUGA1UECAwOUmlvIGRlIEphbmVpcm8xFzAVBgNVBAcMDlJpbyBkZSBK
  YW5laXJvMQswCQYDVQQKDAJUSTELMAkGA1UECwwCVEkxFTATBgNVBAMMDDEwLjEu
  MTI0LjEzMjEiMCAGCSqGSIb3DQEJARYTbmV4dXNAYW5jaW5lLmdvdi5icjCBnzAN
  BgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEAxmQPktPr2t0lrmMz94bYxD4Y/FP9/tpE
  pf0jm4hFix9Fce+mA50jkGo/LIUfGgFqICXvs9dZqyMDyNNb8b2L7i3VoduakVby
  gvFtict5c48+IThuteKVlDNr4On5X5U3PS2RA/oRULEHK37aj7Xry4/ysq/i/d2w
  zmtCre3fQD8CAwEAATANBgkqhkiG9w0BAQsFAAOBgQBwt40OqALuG39BvYTe3Adl
  xCOvZ8TkDVv7NQ+mVXp1SdquChjJGvN7pDEf+e+6Sh1zILYijkWgZhiQnm156W9Q
  5nLMuSx4cX4UUA8H1HtiW8BmF7CV1Uw/iJAnlcYrfwHIil5H9HJ7XsN70Z3fdaEC
  RG0YpY+kyb0sj2pNVGoyjw==
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
