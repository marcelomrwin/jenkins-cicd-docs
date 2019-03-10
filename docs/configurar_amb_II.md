### Inserindo novos nós no jenkins
![](images/fig17-enter-config.png)<br/>
### 21. Clique novamente **Gerenciar Jenkins**
![](images/fig18-config-node.png)
### 22. Clique em **Gerenciar nós***
![](images/fig19-new-node.png)
### 23. Clique em **Novo nó**
![](images/fig20-new-node.png)
### 24. Informe o nome do nó no campo **Nome do nó**
  - selecione **Permanent Agent**
  - clique **OK**
![](images/fig21-new-node.png)
### 25. Preencha o formulário com as informações abaixo:
  - **Nome** jenkins-node1
  - **Descrição** Maven Jenkins Node 1
  - **Número de executores** 2
  - **Diretório remoto** /home/jenkins
  - **Rótulos** maven node1
  - **Uso** Use this node as much as possible
  - **Método de lançamento** Launch agent agents via SSH
    - **_Host_** 10.1.124.129
    - **_Host Key Verification Strategy_** Non verifying Verification Strategy
  - **Disponibilidade** Keep this agent online as much as possible

![](images/fig22-new-node.png)
### 26. Clique em *Metodo de lançamento* -> *Credentials* -> *Add* -> **_Jenkins_**
![](images/fig23-new-node.png)
### 27. Preencha o formulário conforme abaixo:
  - **Domain** Global credentials (unrestricted)
  - **Kind** SSH Username with private key
  - **Scope** Global...
  - **Username** jenkins
  - Marque o campo **Private Key** -> **_Enter directly_**.
  - Dentro deste campo deve ser informada a chave privada do usuário. Durante a instalação o ansible copiou no diretório buffer o arquivo jenkins-master-id_rsa.
  - copie o conteúdo do arquivo para o campo **Key** `cat buffer/jenkins-master-id_rsa`
  - clique em **Add**

![](images/fig24-new-node.png)
### 28. De volta a janela anterior selecione a credential criada.
### 29. Clique em **Salvar**
![](images/fig25-new-node.png)
### 30. Selecione o novo nó criado clicando sobre **jenkins-node1**
  - Se o jenkins não realizar a conexão com o servidor clique no botão **Relaunch agent** para estabelecer a conexão entre o master e o node.
### 31. Repetir os passos [21-30] para inserir o nó *jenkins-node2*
  - Alterar referências de nomes de node1 para node2. Onde se lê jenkins-node1 deve ser alterado para jenkins-node2.
  - Utilizar o ip do Host 10.1.124.130
  - Credenciais utilizar jenkins
![](images/fig26-new-node.png)

![](images/fig27-new-node.png)
### 32. Clique no link **jenkins-node2**
Caso seja necessário clique em **Relaunch agent**
![](images/fig28-new-node.png)
### 33. Na lista de Nós clique no **master**
![](images/fig29-new-node.png)
### 34. Clique em **Configurar**
![](images/fig30-new-node.png)
### 35. Preencha o formulário com as seguintes informações:
  - **Número de executores** 0
  - **Rótulos** master
  - **Uso** Deixar o processamento para jobs vinculadas
  - clique em **Salvar**

### Configurações Globais

![](images/fig31-global.png)
### Clique em **Gerenciar Jenkins** -> **Global Tool Configuration**

![](images/fig32-global.png)
### Na sessão JDK clique em **Adicionar JDK***

![](images/fig33-global.png)
### Configure conforme abaixo:
  - desmarque o campo **Instalar automaticamente**
  - no campo **Nome** informe `JDK_1.8`
  - no campo **JAVA_HOME** informe `/usr/lib/jvm/java-1.8.0-openjdk/`
  - ![](images/fig34-global.png)

![](images/fig35-global.png)
### Navegue até a sessão do Maven e clique em **Adicionar Maven**

![](images/fig36-global.png)
### Configure conforme abaixo:
 - desmarque o campo **Instalar automaticamente**
 - no campo **Nome** informe `M3`
 - no campo **MAVEN_HOME** informe `/opt/apache-maven-3.5.4`

![](images/fig37-global.png)
### Clique em **Save**
