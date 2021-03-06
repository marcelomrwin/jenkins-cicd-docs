### Adicionar bibliotecas do Groovy

Para adicionar as bibliotecas compartilhadas de funcionalidades para o pipeline siga a orientação abaixo:
- Faça o clone do projeto github
  ```bash
  git clone https://github.com/marcelomrwin/groovy_libs.git
  ```
- Acesse a pasta do repositório
  ```bash
  cd groovy_libs
  ```
- Remova as referências ao git e gere um novo compartilhamento
  ```bash
  rm -rf .git
  git init  
  ```
- Com o usuário jenkins logado no repositório GIT crie um novo repositório.
![](/images/fig79.png)</br>

![](/images/fig80.png)</br>
  - Utilize o nome do repositório **groovy_libs**
  - Clique em **Criar repositório**  

- Configure o servidor remoto para o serviço GIT Local. No ambiente de homologação é um servidor GOGS
  ```bash
  git remote add origin https://10.1.123.206/jenkins/groovy_libs.git
  ```
  **No exemplo acima o endereço 10.1.123.206 corresponde ao IP do servidor onde o servidor GIT está em execução. Caso utilize um outro IP fazer o ajuste do endereço correto**
- Edite o arquivo vars/getSonarQubeURL.groovy para refletir a URL do servidor SonarQube.
  ```groovy
  #!/usr/bin/env groovy

  def call() {
    "http://10.1.123.189:9000"
  }
  ```
  Salve o arquivo com o novo conteúdo.

- Submeta o código ao repositório local do git
  ```bash
  git add --all
  git commit -am 'first commit'
  git push -u origin master
  ```

### Resolução de problemas
Caso o GIT exiba mensagens de problema com o certificado do servidor Gogs execute o comando abaixo:
```
git config http.sslVerify false
```

### Configurando a biblioteca no Jenkins
Acesse **Gerenciar Jenkins &rarr; Configurar o sistema**

Na sessão **Global Pipeline Libraries** clique em **Adicionar**
![](/images/fig81.png)

Preencha o formulário conforme os dados abaixo:
![](/images/fig82.png)</br>
- **Name:** groovy-libs
- **Default version:** master
- **Marcar Load implicity**
- **Selecione Modern SCM**
- **Selecione GIT**
- **Project Repository:** http://10.1.123.206/jenkins/groovy_libs.git
- **Credentials:** Selecione Jenkins LDAP

Em Behaviours clique em **Add &rarr; Clean before checkout**</br>
![](/images/fig113.png)</br>


- Clique em **Salvar**
