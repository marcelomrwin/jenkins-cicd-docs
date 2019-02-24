##Instalação e configuração do SonarQube {docsify-ignore}

### Instalando as dependências:
```bash
ansible-galaxy install lean_delivery.java
ansible-galaxy install anxs.postgresql
ansible-galaxy install jdauphant.ssl-certs
ansible-galaxy install nginxinc.nginx
ansible-galaxy install lean_delivery.sonarqube
```

### Instalando através do ansible
```bash
cd ansible
cd sonarqube
ansible-playbook -i hosts playbook.yml -vv
```
### Acessando o Sonarqube
Após a instalação acesse https://192.168.56.160. Um usuário administrativo foi criado. O login e senha são `admin`
