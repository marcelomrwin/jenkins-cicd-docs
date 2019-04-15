### Instalação das seguintes ferramentas de suporte
- Servidor Git - GOGS
- Servidor SVN - Subversion
- Servidor LDAP - OpenLDAP
- Servidor NFS
- Servidor Docker

### Instalar a Role do NFS
```
ansible-galaxy install geerlingguy.nfs --force
```

### Instalar a Role do Docker
```
ansible-galaxy install geerlingguy.docker --force
ansible-galaxy install geerlingguy.pip --force
```

### Executar o seguinte playbook do ansible
Edite o arquivo playbook-vars.yaml e hosts-vmware estejam refletindo a infraestrutura atual. Verifique e valide os nomes das máquinas e IPs.

```
cd ansible/tools
ansible-playbook -i hosts-vmware playbook.yml -vv
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

* Se desejar pode também criar a conta inicial selecionando **Configurações da conta de administrador**
![](/images/fig78.png)

* Clique em **Instalar Gogs**

![](/images/fig40-gogs.png)
Clique em **Registrar** e preencha o formulário
  - **Usuário**: gogs
  - **E-mail**: gogs@cicd.local
  - **Senha**: gogs
  - **Digite novamente**: gogs

*Caso deseje utilize as informações que desejar para criar o usuário, tome nota do usuário e da senha escolhida*

![](/images/fig41-gogs.png)
* clique em entrar e informe o usuário e senha criados acima.

### Acessar menu de configuração
![](/images/fig63-ldap-gogs.png)

- No canto superior direito, acessar o menu e selecionar ***Painel do administrador***

![](/images/fig44-ldap-gogs.png)
### Configurar LDAP no Gogs.

Preencha o formulário com as informações:
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

### Instalar e configurar NFS no Jenkins Master
```
cd ansible/tools
ansible-playbook -i hosts-vmware playbook-nfs.yml -vv
```

### Instalar certificados nos nodes do Jenkins
Executar o playbook abaixo para inserir os certificados gerados nos nodes do jenkins
```
cd ansible
ansible-playbook -i hosts-vmware playbook-tools-ssl.yml -vv
```
