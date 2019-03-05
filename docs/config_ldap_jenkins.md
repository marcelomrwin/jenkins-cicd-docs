- Acesse a opção -> **Gerenciar Jenkins** -> **Configurar segurança global**
- Em **Domínio (realm) de segurança** selecione **LDAP**
![](/images/fig42-ldap.png)
- Preencha o formulário com os valores:
  - **Server**: *ldap://192.168.56.140:389*
  - **User search base**: *ou=Users,dc=cicd,dc=local*
  - **User search filter**: *uid={0}*
  - **Group search base**: *ou=Groups,dc=cicd,dc=local*
  - **Group membership**: *Search for LDAP...*
  - **Manager DN**: *cn=ldapadm,dc=cicd,dc=local*
  - **Manager Password**: *password*
  - **Display Name LDAP attribute**: *sn*
  - **Email Address LDAP attribute**: *mail*

- Clique em **Test LDAP settings**
  - Informe user: _jenkins_
  - Informe password: _password_
  - Clique em **Test**

- Informe as variáveis de ambiente clicando em **Environment Properties -> Adicionar**
  - **name:** com.sun.jndi.ldap.connect.timeout
  - **value:** 5000

  - **name:** com.sun.jndi.ldap.read.timeout
  - **value:** 10000

### Configurar a matriz de segurança do Jenkins para utilizar LDAP

- Selecione **Gerenciar Jenkins -> Configurar segurança global**

![](/images/fig43-ldap.png)
- Na área **Autorização** configure conforme acima
  - Para adicionar os grupos clique em **Add user or group...**

Clique em **Salvar**

Caso o jenkins solicite permissões especiais faça o logout do usuário atual e realize um novo login.
- usuário: _jenkins_
- senha: _password_

_este usuário pertence ao domínio do LDAP e faz parte do grupo Devops que para o jenkins corresponde ao grupo de administradores_
