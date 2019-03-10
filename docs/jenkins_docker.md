### Acessar a API remota do Docker (Opcional)
A API remota do docker está acessível através da URL http://10.1.124.131:4243

Um exemplo de como obter as imagens no repositório remoto:
```
curl -X GET http://10.1.124.131:4243/images/json
```

### Configurando o Jenkins para acessar a API do Docker
- Acesse **Gerenciar Jenkins &rarr; Configurar o sistema**
- Navegue até a área de Nuvem (Cloud)

![](/images/fig67-docker.png)
- Clique em **Adicionar uma nova nuvem &rarr; Docker**
- Clique em **Docker Cloud details...**

Preencha o formulário com as informações
![](/images/fig68-docker.png)
  - **Name:** _docker_
  - **Docker Host URI:** _tcp://10.1.124.131:4243_
  - utilize o botão **Test Connection** para garantir que o jenkins consegue comunicação com o servidor Docker
  - **Enabled:** Deixar marcado
  - Clique em **Salvar**

### Configurando uma imagem docker padrão
- Acesse o host support-tools que provê o ambiente Docker. Na configuração parão o acesso se dá através do comando:
```
ssh root@10.1.124.131
```
_obs: Lembre-se de alterar o IP caso tenha modificado a configuração padrão_

- Dentro do host faça o pull da imagem base que será utilizada para gerar artefatos java.
```
docker pull maven:3.5-jdk-8
```
- Verifique a imagem base foi baixada no repositório local
```bash
[root@support-tools ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
maven               3.5-jdk-8           985f3637ded4        4 months ago        635MB
```

- Execute um container utilizando a imagem base.

```
docker run -it --name maven maven:3.5-jdk-8 /bin/bash
```
- Crie o usuário jenkins
```
adduser jenkins
```
- Informe os dados solicitados até a conclusão da criação do usuário.

- Instale o editor de texto e o servidor SSH
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
- Edite o arquivo ~/.m2/settings-docker.xml
```
vi ~/.m2/settings-docker.xml
```
- Remova todas as linhas do arquivo
  - pressione :
  - digite `:1,$d`
  - tecle Enter
- Insira o novo conteúdo do arquivo
  - pressione A (Para entrar em modo de insert/append)
- Copie o cole o seguinte conteúdo dentro do arquivo
```
<?xml version="1.0" encoding="UTF-8"?><settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd"><localRepository>${user.home}/.m2/repository</localRepository><offline>false</offline><profiles><profile><id>default-repository</id><properties><downloadSources>true</downloadSources><downloadJavadocs>true</downloadJavadocs></properties><repositories><repository><id>company-project</id><url>https://10.1.124.132/repository/company-project/</url><releases><updatePolicy>never</updatePolicy></releases><snapshots><enabled>false</enabled></snapshots></repository><repository><id>nexus-release</id><url>https://10.1.124.132/repository/maven-releases/</url><releases><updatePolicy>never</updatePolicy></releases><snapshots><enabled>false</enabled></snapshots></repository><repository><id>nexus-snapshot</id><url>https://10.1.124.132/repository/maven-snapshots/</url><releases><enabled>false</enabled></releases><snapshots><updatePolicy>always</updatePolicy></snapshots></repository><repository><id>public</id><url>https://10.1.124.132/repository/public/</url><releases><updatePolicy>never</updatePolicy></releases><snapshots><enabled>false</enabled></snapshots></repository></repositories><pluginRepositories><pluginRepository><id>nexus-release</id><url>https://10.1.124.132/repository/maven-releases/</url><releases><updatePolicy>never</updatePolicy></releases><snapshots><enabled>false</enabled></snapshots></pluginRepository><pluginRepository><id>nexus-snapshot</id><url>https://10.1.124.132/repository/maven-snapshots/</url><releases><enabled>false</enabled></releases><snapshots><updatePolicy>always</updatePolicy></snapshots></pluginRepository><pluginRepository><id>public</id><url>https://10.1.124.132/repository/public/</url><releases><updatePolicy>never</updatePolicy></releases><snapshots><enabled>false</enabled></snapshots></pluginRepository></pluginRepositories></profile></profiles><servers><server><id>company-project</id><username>jenkins</username><password>password</password></server><server><id>nexus-release</id><username>jenkins</username><password>password</password></server><server><id>nexus-snapshot</id><username>jenkins</username><password>password</password></server><server><id>public</id><username>jenkins</username><password>password</password></server></servers><activeProfiles><activeProfile>default-repository</activeProfile></activeProfiles><mirrors><mirror><id>public</id><name>public</name><url>https://10.1.124.132/repository/public/</url><mirrorOf>*</mirrorOf></mirror></mirrors></settings>
```
- Salve o arquivo
  - pressione `esc`
  - digite `:`
  - digite `x`
  - pressione enter

- Crie a pasta .m2 para o usuário jenkins
```
mkdir -p /home/jenkins/.m2
```
- Copie o arquivo para a pasta .m2 do usuário jenkins
```
cp ~/.m2/settings-docker.xml /home/jenkins/.m2/settings-docker.xml
cp ~/.m2/settings-docker.xml /home/jenkins/.m2/settings.xml
```

- Altere as permissões de acesso para o usuário jenkins
```
chown -R jenkins.jenkins /home/jenkins/.m2/
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
keytool -import -storepass changeit -noprompt -alias nexus -keystore $JAVA_HOME/jre/lib/security/cacerts -trustcacerts -file ca.crt
```
- saia do container docker digitando `exit`
- faça o commit da imagem customizada.
  - digite `docker ps -a`
  ```
  [root@support-tools ~]# docker ps -a
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
  a854c73fe8af        maven:3.5-jdk-8     "/usr/local/bin/mvn-…"   2 hours ago         Exited (0) 8 seconds ago                       maven
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
maven                  3.5-jdk-8           985f3637ded4        4 months ago        635MB
```
