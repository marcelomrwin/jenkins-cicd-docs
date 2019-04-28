### Instalar Nexus Repository
Importe as roles do ansible

```
ansible-galaxy install geerlingguy.apache --force
ansible-galaxy install geerlingguy.java --force
ansible-galaxy install ansible-thoteam.nexus3-oss --force
```

**Certifique-se de utilizar a versão mínima recomendada:**
- geerlingguy.apache, 3.0.3
- ansiblebit.oracle-java, 6.22.8
- ansible-thoteam.nexus3-oss, v2.4.1

**Observação: Antes de executar o playbook garanta que os certificados estão válidos para o endereço final do nexus. Se desejar gerar novos certificados consultar a sessão extra. Caso possua certificados válidos basta substituir os certificados padrão. ca.crt, ca.csr e ca.key**

**Valide e verifique o hosts-vmware e playbook-vars**

Acesse a pasta [nexus](../ansible/nexus) e execute:
```
cd ansible/nexus
ansible-playbook -i hosts-vmware --ask-vault-pass --extra-vars '@passwd.yml' playbook.yml -vv
```
Quando solicitado digite a senha do vault: `password`

Após estes passos o nexus será provisionado na URL https://10.1.123.207. O usuário administrativo padrão é `admin` e a senha `admin123`.
Existe também o usuário `jenkins` com a senha `password`, este usuário está configurado no LDAP.

## _Os passos abaixo servem apenas para verificação, o playbook do ansible já realiza todas estas configurações automaticamente._ {docsify-ignore}
<br/>Continua no passo [Configurando o Jenkins para acesso ao Nexus](#configurando-o-jenkins-para-acesso-ao-nexus)

### Configuração do LDAP no Nexus
![](/images/fig50-nexus-ldap.png)
  - Acessar configurações
  - Security -> Realms
  - Certificar que o Realm LDAP Realm está na coluna **Active**
  - Clique em **Save**

![](/images/fig45-nexus-ldap.png)
  - Acessar configurações
  - Security -> LDAP
  - Clique em **Create connection**

- Na aba *Connection* preencha o formulário com os dados:
  - **Name**: CICD LDAP
  - **LDAP server address**: ldap - 10.1.124.131 - 389
  - **Search base**: dc=cicd,dc=local
  - **Authentication method**: Simple Authentication
  - **Username or DN**: cn=ldapadm,dc=cicd,dc=local
  - **Password**: `password`

![](/images/fig46-nexus-ldap.png)
- Na aba *User and group* preencha o formulário com os dados:
    - **Base DN**: ou=Users
    - **Object class**: inetOrgPerson
    - **User ID attribute**: uid
    - **Real name attribute**: sn
    - **Email attribute**: mail
    - **Map LDAP groups as roles**: *deixar marcado*
    - **Group type**: Dynamic Groups
    - **Group member of attribute**: memberOf

_É importante clicar em **Verify user mapping** para testar as configurações_
_É importante clicar em **Verify login** para testar as configurações_

### Criação de Roles no Nexus
![](/images/fig47-nexus.png)
- Acessar configurações
- Security -> Roles
- Clique em **Create role**->**External role mapping**->**LDAP**

### Criando a Role ***Developer***

**Importante** - Por padrão o Nexus já traz uma ROLE com o nome de developers, exclua esta role antes de criar a nova.

![](/images/fig49-nexus.png)
No formulário apresentado preencha com os seguintes dados:
- **Mapped Role**: Developers
- **Role Name**: Developers
- **Role description**: All Developers
- **Privileges**:
  Mova para a coluna **Given** os valores:
    - nx-repository-view-maven2-*-read
    - nx-repository-admin-maven2-company-project-*
    - nx-search-read
- Clique em **Create role**



### Criando a Role ***Devops***
![](/images/fig48-nexus.png)
Repita o acesso Configurações -> Security -> Roles -> **Create role mapping**->**LDAP**
No formulário apresentado preencha com os seguintes dados:
- **Mapped Role**: Devops
- **Role Name**: Devops
- **Role description**: Devops
- **Roles**:
  Mova para a coluna **Contained** o valor:
    - nx-admin    
- Clique em **Create role**


### Adicionando novos repositórios no Nexus
![](/images/fig59-nexus.png)
Acesse **Configurações** -> Repositories
Clique em **Create repository**
- Selecione o tipo **maven2 (proxy)**

![](/images/fig60-nexus.png)
- No campo **Name** informe: _redhat-ga-repository_
- No campo **Remote storage** informe: _https://maven.repository.redhat.com/ga_
- No campo **Blob store** selecione _default_
- Clique em **Create repository**

Repita os passos acima para criar outros repositórios considerando os dados:
- **Name**: jaspersoft<br/>
  **Remote storage**: https://jaspersoft.artifactoryonline.com/jaspersoft/jaspersoft-repo/

- **Name**: jboss<br/>
  **Remote storage**: https://repository.jboss.org/nexus/content/groups/public-jboss/

- **Name**: jcenter<br/>
  **Remote storage**: https://jcenter.bintray.com/

- **Name**: earlyaccess<br/>
  **Remote storage**: https://maven.repository.redhat.com/earlyaccess/all/

![](/images/fig61-nexus.png)
Na lista de repositórios selecione o **public**

![](/images/fig62-nexus.png)
Mova para a coluna **Members** todos os repositórios presentes na coluna **Available**
Clique em **Save**

### Configurando o Jenkins para acesso ao Nexus
![](/images/fig75-nexus-jenkins.png)</br>
- Acesse **Gerenciar Jenkins &rarr; Configurar o Sistema**
- Navegue até a sessão **Sonatype Nexus**
- Clique em **Add Nexus Repository Manager Server**
- Selecione **Nexus Repository Manager 3.x Server**

![](/images/fig76-nexus-jenkins.png)</br>
Preencha o formulário com os dados abaixo:
- **Display Name:** Nexus
- **Server ID:** nexus
- **Server URL:** https://10.1.123.207/
- **Credentials:** Selecione Jenkins LDAP
- clique em **Test connection** para assegurar que a comunicação com o servidor nexus está funcional.
- clique em **Salvar**

*Ignore a mensagem de advertência que solicita features da versão PRO do Sonatype Nexus*
