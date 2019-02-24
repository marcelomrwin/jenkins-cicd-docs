- Acesse a opção -> **Gerenciar Jenkins** -> **Configurar segurança global**
- Em **Domínio (realm) de segurança** selecione **LDAP**
![](../../images/fig42-ldap.png)
- Preencha o formulário com os valores:
  - **Server**: *ldap:192.168.56.140:389*
  - **User search base**: *ou=Users,dc=cicd,dc=local*
  - **User search filter**: *uid={0}*
  - **Group search base**: *ou=Groups,dc=cicd,dc=local*
  - **Group membership**: *Search for LDAP...*
  - **Manager DN**: *cn=ldapadm,dc=cicd,dc=local*
  - **Manager Password**: *password*
  - **Display Name LDAP attribute**: *sn*
  - **Email Address LDAP attribute**: *mail*

![](../../images/fig43-ldap.png)
- Na área **Autorização** configure conforme acima
  - Para adicionar os grupos clique em **Add user or group...**
