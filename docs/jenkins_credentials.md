## Configurando credenciais que serão usadas para acessar o repositório Nexus {docsify-ignore}

![](/images/fig51-jenkins-credential.png)
Acessar **Manage Jenkins** -> **Credentials** -> _clicar sobre o link_ **(global)**

![](/images/fig52-jenkins-credential.png)
Clique em **Add Credentials**

![](/images/fig53-jenkins-credential.png)
No formulário informe os seguintes dados:
- **Kind**: Username with password
- **Scope**: Global ...
- **Username**: jenkins
- **password**: `password`
- **ID**: jenkinsldap
- **Description**: Jenkins LDAP

_O usuário acima deve corresponder a usuário presente na base AD/LDAP_
