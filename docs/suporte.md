### Instalação das seguintes ferramentas de suporte
- Servidor Git - GOGS
- Servidor SVN - Subversion
- Servidor LDAP - OpenLDAP
- Servidor NFS
- Servidor Docker

### Instalar a Role do NFS
```
ansible-galaxy install geerlingguy.nfs
```

### Instalar a Role do Docker
```
ansible-galaxy install geerlingguy.docker
ansible-galaxy install geerlingguy.pip
```

### Executar o seguinte playbook do ansible
```
cd ansible/tools
ansible-playbook -i hosts playbook.yml -vv
```

As seguintes URLs estarão disponíveis:
- https://10.1.124.131 - GOGS
- https://10.1.124.131:81/svn/reposvn - Subversion

Os seguintes serviços estarão disponíveis:
- NFS server
- LDAP Server
- Docker API Service

### Configuração do GOGS
![](/images/fig38-gogs.png)
* Configurar o banco como PostgreSQL
  - **usuário**: gogs
  - **senha**: gogs
  - **Nome do banco**: gogs
![](/images/fig39-gogs.png)
* **URL do aplication** inserir `https://10.1.124.131/`
* Clique em **Instalar Gogs**

![](/images/fig40-gogs.png)
Clique em **Registrar** e preencha o formulário
  - **Usuário**: gogs
  - **E-mail**: gogs@cicd.local
  - **Senha**: gogs
  - **Digite novamente**: gogs

![](/images/fig63-ldap-gogs.png)
### Acessar menu de configuração
- No canto superior direito, acessar o menu e selecionar ***Painel do administrador***

![](/images/fig44-ldap-gogs.png)
### Configurar LDAP no Gogs. Preencha o formulário com as informações:
  - **Authentication Type**: LDAP (simple auth)
  - **Security Protocol**: Unecrypted
  - **Host**: 10.1.124.131
  - **Port**: 389
  - **User DN**: `uid=%s,ou=Users,dc=cicd,dc=local`
  - **User Filter**: `(&(objectClass=posixAccount)(uid=%s))`
  - **Admin Filter**: `(memberOf=CN=Devops,OU=Groups,DC=cicd,DC=local)`
  - **Username Attribute**: uid
  - **First Attribute**: sn
  - **Surname Attribute**: sn
  - **Email Attribute**: mail

Clique em **Add New Source**


*Caso deseje utilize as informações que desejar para criar o usuário, tome nota do usuário e da senha escolhida*

![](/images/fig41-gogs.png)
* clique em entrar e informe o usuário e senha criados acima.

### Acessar a API remota do Docker
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


docker pull maven:3.5-jdk-8
adduser jenkins
