### Importando a aplicação de exemplo
Realize o clone da aplicação no GitHub
```
git clone https://github.com/marcelomrwin/hello-world-greeting.git
```
Acesse a pasta do projeto
```
cd hello-world-greeting
```
Remova as referências ao repositório git
```
git remote remove origin
```

Edite o arquivo jenkinsFile e ajuste o valor da propriedade _NEXUS_URL_ apontando para o endereço nexus do seu ambiente.

Crie um repositório no GOGS local
![](/images/fig83.png)
Use o **Nome do repositório** hello-world-greeting</br>
Clique em **Criar repositório**

Adicione o novo repositório no projeto hello-world-greeting
```
git remote add origin http://10.1.123.206/jenkins/hello-world-greeting.git
```
Submeta o código local para o servidor GOGS
```
git push -u origin master
```
*Caso o GOGS solicite usuário e senha informe o usuário <b>jenkins</b> e a senha <b>password</b>*

### Criar um projeto Multibranch no Jenkins
No Jenkins selecione **Novo job**
- Informe o nome *hello-world-greeting*
- Selecione Multibranch Pipeline
![](/images/fig84.png)

Na sessão **Branch Sources**
- Clique em **Add source**
- Selecione **Git**
![](/images/fig85.png)

No formulário **Branch Sources** informe
- **Project Repository:** http://10.1.123.206/jenkins/hello-world-greeting.git
- **Credentials:** Selecione Jenkins LDAP
- Deixe os demais campos com valor padrão
- Certifique-se de que na sessão **Build Configuration** o **Mode** selecionado seja *by Jenkinsfile* e **Script Path** seja *Jenkinsfile*.
![](/images/fig86.png)
Clique em **Save**

O jenkins iniciará uma varredura no repositório git procurando por branches que possuam um arquivo Jenkinsfile.
Uma vez que o Jenkins localizar branches conforme acima ele iniciará um build job para cada um.
Observe o job executado e o link para acessar o SonarQube.
![](/images/fig91.png)</br>

Acesse o link http://10.1.124.133:9000/dashboard?id=com.example%3Agreetings para ter acesso ao relatório do SonarQube
![](/images/fig92.png)</br>

O jenkins exibirá uma tela com um resumo do pipeline conforme abaixo:
![](/images/fig93.png)</br>

### Configurando um Webhook no Gogs
Acesse o Gogs https://10.1.123.206
- acesse o projeto jenkins/hello-world-greeting
- clique em **Configurações**
- no menu do lado esquerdo clique em **Webhooks**
- clique em **Adicionar Webhook**
  - selecione **GOGS**
![](/images/fig98.png)</br>

No campo **URL de Payload** informe https://10.1.124.128/gogs-webhook/?job=hello-world-greeting
Clique em **Adicionar Webhoook**</br>
![](/images/fig99.png)</br>

Clique no link do novo webhook para validar se está funcional.
![](/images/fig100.png)</br>

No canto inferior direito clique sobre **Entrega de teste** para validar a URL.
![](/images/fig101.png)</br>

Verifique se a entrega foi feita com sucesso conforme baixo.
![](/images/fig102.png)</br>

Se alterarmos o Quality Gate para não tolerar warnings, ou seja, mudarmos o valor de warning para 1 conforme janela abaixo
![](/images/fig103.png)</br>

Ao executarmos o pipeline novamente veremos que o processo falhará exatamente pela identificação de alerta do SonarQube
![](/images/fig104.png)</br>

Abaixo o artefato gerado com sucesso publicado no repositório Nexus
![](/images/fig105.png)</br>

### Resumo da aplicação de exemplo

A aplicação está devidamente inserida num ambiente de Continuos Integration (CI). Cada nova operação de push no servidor SCM do GOGS gerará uma nova build automática no Jenkins.

O Jenkins utiliza um pipeline que realiza:
- Download do código fonte
- Build e testes unitários
- Análise estática de código fonte
- Testes de integração
- Publicação do artefato gerado no Nexus em um repositório customizado do Nexus.
