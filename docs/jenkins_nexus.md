<a name="top"></a>
## Configurando o Jenkins para acesso ao Nexus e repositórios Maven {docsify-ignore}
![](/images/fig54-jenkins.png)
Acessar **Manage Jenkins** -> **Managed files**

![](/images/fig55-jenkins.png)
Clique em **Add new Config**

![](/images/fig56-jenkins.png)
- Selecione **Maven settings.xml**
- No campo **ID** informe _maven-settings_
- clique em **Submit**

![](/images/fig57-jenkins.png)
No formulário preencha com os seguintes dados:
- **ID**: maven-settings
- **Name**: Nexus Config
- **Comment**: user settings
- **Server Credentials** cadastre três repositórios:
  - company-project
  - nexus-release
  - nexus-snapshot
Para todos selecione a Credentials **Jenkins LDAP**

![](/images/fig58-jenkins.png)
No campo **Content** preencha com o conteúdo abaixo:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
      	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      	xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">

  <localRepository>${user.home}/.m2/repository</localRepository>
  <offline>false</offline>
  <profiles>
	<profile>
      <id>default-repository</id>
      <properties>
        <downloadSources>true</downloadSources>
        <downloadJavadocs>true</downloadJavadocs>
      </properties>
      <repositories>

        <repository>
          <id>company-project</id>
          <url>https://192.168.56.150/repository/maven-releases/</url>
          <releases>
            <updatePolicy>never</updatePolicy>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
        </repository>

    	  <repository>
          <id>nexus-release</id>
     	    <url>https://192.168.56.150/repository/maven-releases/</url>
          <releases>
            <updatePolicy>never</updatePolicy>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
    	  </repository>

        <repository>
          <id>nexus-snapshot</id>
          <url>https://192.168.56.150/repository/maven-snapshots/</url>
          <releases>
            <enabled>false</enabled>
          </releases>
          <snapshots>
            <updatePolicy>always</updatePolicy>
          </snapshots>
        </repository>

        <repository>
          <id>public</id>
     	    <url>https://192.168.56.150/repository/public/</url>
          <releases>
            <updatePolicy>never</updatePolicy>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
    	  </repository>
      </repositories>
      <pluginRepositories>
        <pluginRepository>
          <id>nexus-release</id>
          <url>https://192.168.56.150/repository/maven-releases/</url>
          <releases>
            <updatePolicy>never</updatePolicy>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
        </pluginRepository>
        <pluginRepository>
          <id>nexus-snapshot</id>
           <url>https://192.168.56.150/repository/maven-snapshots/</url>
          <releases>
            <enabled>false</enabled>
          </releases>
          <snapshots>
            <updatePolicy>always</updatePolicy>
          </snapshots>
        </pluginRepository>
        <pluginRepository>
          <id>public</id>
           <url>https://192.168.56.150/repository/public/</url>
          <releases>
            <updatePolicy>never</updatePolicy>
          </releases>
          <snapshots>
            <enabled>false</enabled>
          </snapshots>
        </pluginRepository>        
      </pluginRepositories>
    </profile>
  </profiles>

  <servers>
    <server>
      <id>company-project</id>
      <username></username>
      <password></password>
    </server>
    <server>
      <id>nexus-release</id>
      <username></username>
      <password></password>
    </server>
    <server>
      <id>nexus-snapshot</id>
      <username></username>
      <password></password>
    </server>
  </servers>

  <activeProfiles>
    <activeProfile>default-repository</activeProfile>
  </activeProfiles>

</settings>
```

- Clique em **Submit**

<div class="container">  
  <div class="bottomright">
    <a href="#top">Ir para o topo</a> | <a href="/#/">Início</a>
  </div>
</div>
