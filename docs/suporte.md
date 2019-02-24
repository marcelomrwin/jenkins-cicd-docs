### Instalação das seguintes ferramentas de suporte
- Servidor Git - GOGS
- Servidor SVN - Subversion
- Servidor LDAP
- Servidor NFS

### Instalar a Role do NFS
```
ansible-galaxy install geerlingguy.nfs
```

### Executar o seguinte playbook do ansible
```
cd ansible/tools
ansible-playbook -i hosts playbook.yml -vv
```

As seguintes URLs estarão disponíveis:
- https://192.168.56.140 - GOGS
- https://192.168.56.140:81/svn/reposvn - Subversion

Os seguintes serviços estarão disponíveis:
- NFS server
- LDAP Server

### Configuração do GOGS
![](../../images/fig38-gogs.png)
* Configurar o banco como PostgreSQL
  - **usuário**: gogs
  - **senha**: gogs
  - **Nome do banco**: gogs
![](../../images/fig39-gogs.png)
* **URL do aplication** inserir `https://192.168.56.140/`

![](../../images/fig40-gogs.png)
Clique em **Registrar** e preencha o formulário
  - **Usuário**: gogs
  - **E-mail**: gogs@cicd.local
  - **Senha**: gogs
  - **Digite novamente**: gogs

![](../../images/fig44-ldap-gogs.png)
### Configurar LDAP no Gogs. Preencha o formulário com as informações:
  - **Authentication Type**: LDAP
  - **Security Protocol**: Unecrypted
  - **Host**: 192.168.56.140
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

![](../../images/fig41-gogs.png)
* clique em entrar e informe o usuário e senha criados acima.
