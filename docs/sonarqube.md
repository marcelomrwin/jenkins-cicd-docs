##Instalação e configuração do SonarQube {docsify-ignore}

### Instalando as dependências:

```bash
ansible-galaxy install lean_delivery.java --force
ansible-galaxy install anxs.postgresql --force
ansible-galaxy install jdauphant.ssl-certs --force
ansible-galaxy install nginxinc.nginx --force
ansible-galaxy install lean_delivery.sonarqube --force
```

Verique se está instalado a versão mínima recomendada.
- lean_delivery.java, 2.0.1
- anxs.postgresql, v1.10.1
- jdauphant.ssl-certs, v1.7.1
- nginxinc.nginx, 0.11.0
- lean_delivery.sonarqube, 1.5.1

### Instalando através do ansible
```bash
cd ansible
cd sonarqube
ansible-playbook -i hosts-vmware playbook.yml -vv
ansible-playbook -i hosts-vmware playbook-nginx.yml -vv
```

*Devido a quantidade de plugins que a instalação realiza é necessário baixar muitos arquivos e por este motivo pode levar de 10 a 15 minutos*

### Acessando o Sonarqube
Após a instalação acesse https://10.1.123.189 ou http://10.1.123.189:9000.

![](/images/fig70-sonarqube.png)</br>
Faça login com o usuário administrativo, login e senha `admin`

O SonarQube já está configurado para acessar o LDAP. Os usuários que fazem parte do grupo sonar-administrators no LDAP também são automaticamente administradores do SonarQube. O usuário jenkins faz parte do grupo. Caso queira poderá utilizar o usuário `jenkins` com a senha `password`.

### Gerando um token para uso no Jenkins
![](/images/fig71-sonarqube.png)</br>
- Acesse as configurações do usuário clicando no canto superior direito no nome do usuário e em seguida _My Account_
- Clique na aba **Security**

![](/images/fig72-sonarqube.png)</br>
- Informe um nome para o token e clique em **Generate**
- Anote o valor do token, no exemplo _8133fb3782de82ebf0ecc4d05fd8781f9c2d0cae_

### Adicionando o SonarQube no Jenkins
Acesse o Jenkins e clique em **Gerenciar Jenkins &rarr; Configurar o sistema**

![](/images/fig73-sonarqube.png)</br>
- Acesse a área **SonarQube servers**
- Clique em **Add SonarQube**

![](/images/fig74-sonarqube.png)</br>
No formulário informe os dados conforme segue:
- **Name:** SonarQube
- **Server URL:** http://10.1.123.189:9000
- **Server authentication token:** *Informe o token gerado pelo sonarqube acima*
- Clique em **Salvar**

### Configurando Webhook no SonarQube
Acesse o SonarQube https://10.1.123.189 e clique em **Administration &rarr; Configuration &rarr; Webhooks**
![](/images/fig87.png)</br>

Clique em **Create** No canto superior direito
![](/images/fig88.png)</br>

No formulário informe os dados:
- **Name:** jenkins
- **URL:** http://10.1.123.208:8080/sonarqube-webhook
- Clique em **Create**</br>
![](/images/fig89.png)</br>

### Ajustando permissões no SonarQube
Acesse o SonarQube https://10.1.123.189 e clique em **Administration &rarr; Security &rarr; Global Permissions**
Conceda permissão **Execute Analysis** ao usuário sonar-administrators</br>
![](/images/fig90.png)</br>

### Customizando um Quality Gate para a aplicação de exemplo
Acesse o SonarQube https://10.1.123.189 e clique em **Quality Gates**
- Clique em **Sonar way** e clique em **copy** no canto superior direito
![](/images/fig94.png)</br>
- Informe em name **Sonar Custom**
- Clique em **Copy**

Um novo Quality Gate será criado. Clique sobre o Quality Gate **Sonar Custom** no lado esquerdo.</br>
Clique em Add Condition
![](/images/fig95.png)</br>

![](/images/fig96.png)</br>
- **Metric:** Major Issues
- **Operator:** is greater than
- **Warning:** 10
- **Error:** 30
Clique em **Add Condition**

![](/images/fig97.png)</br>
Selecione o Quality Gate **Sonar Custom**</br>
Clique em **Set as Default**
