##Instalação e configuração do SonarQube {docsify-ignore}

### Instalando as dependências:

```
ansible-galaxy install lean_delivery.java
ansible-galaxy install anxs.postgresql
ansible-galaxy install jdauphant.ssl-certs
ansible-galaxy install nginxinc.nginx
ansible-galaxy install lean_delivery.sonarqube
```

Verique se está instalado a versão mínima recomendada.
- lean_delivery.java, 2.0.1
- anxs.postgresql, v1.10.1
- jdauphant.ssl-certs, v1.7.1
- jdauphant.nginx, v2.21.2
- lean_delivery.sonarqube, 1.5.1

### Instalando através do ansible
```bash
cd ansible
cd sonarqube
ansible-playbook -i hosts-vmware playbook.yml -vv
```
### Acessando o Sonarqube
Após a instalação acesse https://10.1.124.133.


![](/images/fig70-sonarqube.png)</br>
Faça login com o usuário administrativo, login e senha `admin`

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
- **Server URL:** http://10.1.124.133:9000
- **Server authentication token:** *Informe o token gerado pelo sonarqube acima*
- Clique em **Salvar**
