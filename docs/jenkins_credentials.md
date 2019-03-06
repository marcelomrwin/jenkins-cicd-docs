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

Clique em **Salvar**

_O usuário acima deve corresponder a usuário presente na base AD/LDAP_

### Inserindo credenciais do GitHub / GitLab

Para inserir credenciais de acesso a repositórios GIT como GitHub ou GitLab primeiramente é necessário a geração de tokens de acesso.

Existem excelentes guias para geração de tokens de acesso;
- GitHub
  - [tutorial I](https://help.github.com/en/articles/authorizing-a-personal-access-token-for-use-with-a-saml-single-sign-on-organization)
  - [tutorial II](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)
- GitLab
  - [tutorial](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html)

- No jenkins selecione **Credentials &rarr; System &rarr; Global credentials (unrestricted)**
- Clique em **Add Credentials**
![](/images/fig64-jenkins-credentials.png)
  - selecione o Kind: _Secret text_
  - scope: _Global..._
  - Em Secret informe a chave gerada pelo repositório Git.
  - Informe um ID
  - Adicione uma descrição clara
  - Clique em **OK**
