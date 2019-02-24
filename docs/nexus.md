### Instalar Nexus Repository
Importe as roles do ansible

```
ansible-galaxy install geerlingguy.apache
ansible-galaxy install ansiblebit.oracle-java
ansible-galaxy install ansible-thoteam.nexus3-oss
```

O playbook ansible-nexus necessita de uma alteração antes de ser executado. Para isto edite o arquivo `~/.ansible/roles/ansible-thoteam.nexus3-oss/tasks/nexus_install.yml`
Na linha 419 edite a tarefa **Archive Scripts** adicionando o conteúdo <br/>
`vars:
   ansible_become: no
`<br/>
O conteúdo final da tarefa será:
```yaml
- name: Archive scripts
  local_action:
    module: archive
    path: "{{ role_path }}/files/groovy/*"
    dest: "/tmp/nexus-upload-groovy-scripts.tar.gz"
  vars:
     ansible_become: no
  run_once: true
```
*Observação: Este procedimento é necessário devido a um BUG no playbook de donfiguração. Um request será endereçado para o playbook original para evitar estes passos em futuras versões do playbook.*

Acesse a pasta [nexus](../ansible/nexus) e execute:
```
ansible-playbook -i hosts --ask-vault-pass --extra-vars '@passwd.yml' playbook.yml
```
Quando solicitado digite a senha do vault: `password`

Após estes passos o nexus será provisionado na URL https://192.168.56.150. O usuário administrativo padrão é `admin` e a senha `admin123`.
Existe também o usuário `jenkins` com a senha `password`.

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
  - **LDAP server address**: ldap - 192.168.56.140 - 389
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
![](/images/fig49-nexus.png)
No formulário apresentado preencha com os seguintes dados:
- **Mapped Role**: Developers
- **Role Name**: Developers
- **Role description**: All Developers
- **Privileges**:
  Mova para a coluna **Given** os valores:
    - all-repos-read
    - company-project-deploy
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
- Selecione o tipo **maven2 (proxy)**

### Adicionando novos repositórios no Nexus
![](/images/fig59-nexus.png)
Acesse **Configurações** -> Repositories
Clique em **Create repository**

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