##Instalação e configuração do SonarQube {docsify-ignore}

### Instalando as dependências:

```
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
ansible-playbook -i hosts-vmware playbook.yml -vv
ansible-playbook -i hosts-vmware playbook-nginx.yml -vv
```
### Acessando o Sonarqube
Após a instalação acesse https://10.1.124.133. Um usuário administrativo foi criado. O login e senha são `admin`
