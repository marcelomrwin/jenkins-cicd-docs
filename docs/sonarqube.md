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

Um usuário administrativo foi criado com login e senha `admin`
