### Generate jenkins plugin list

https://10.1.124.128/script
```
Jenkins.instance.pluginManager.plugins.each{
  plugin ->
    println ("${plugin.getShortName()}:${plugin.getVersion()}")
}
```

### Para gerar o password das contas utilizar
```
mkpasswd --method=sha-512
```

### Gerar certificados para o Apache

**Step 1: Generate Private Key**
```
openssl genrsa -out ca.key 1024
```
**Step 2: Generate Certificate Request File**
```
openssl req –new –key ca.key –out ca.csr
```
**Step 3: Generate the Self Signed Certificate**
```
openssl x509 –req –days 365 –in ca.csr –signkey ca.key –out ca.crt
```

### Para ignorar a geração de arquivos .retry no ansible utilizar
```
ANSIBLE_RETRY_FILES_ENABLED=0 ansible-playbook ...
```


### [Jenkins Best Practices](https://wiki.jenkins.io/display/JENKINS/Jenkins+Best+Practices)
- Always secure Jenkins
- In larger systems, don't build on the master
- Backup Jenkins Home regularly
- Limit project names to a sane (e.g. alphanumeric) character set
- Use "file fingerprinting" to manage dependencies
- The most reliable builds will be clean builds, which are built fully from Source Code Control
- Integrate tightly with your issue tracking system, like JIRA or bugzilla, to reduce the need for maintaining a Change Log
- Integrate tightly with a repository browsing tool like FishEye if you are using Subversion as source code management tool
- Always configure your job to generate trend reports and automated testing when running a Java build
- Set up Jenkins on the partition that has the most free disk-space
- Archive unused jobs before removing them.
- Setup a different job/project for each maintenance or development branch you create
- Prevent resource collisions in jobs that are running in parallel.
- Avoid scheduling all jobs to start at the same time
- Set up email notifications mapping to ALL developers in the project, so that everyone on the team has his pulse on the project's current status
- Take steps to ensure failures are reported as soon as possible
- Write jobs for your maintenance tasks, such as cleanup operations to avoid full disk problems.
- Tag, label, or baseline the codebase after the successful build

### Vagrant operations
```
vagrant up
vagrant halt
vagrant destroy -f (vagrant destroy -f support-tools)
vagrant snapshot push (vagrant snapshot push support-tools)
vagrant snapshot restore (vagrant snapshot restore support-tools push_1550956042_1311)
vagrant snapshot delete
vagrant snapshot pop
```
Using snapshots
```
vagrant snapshot restore jenkins-master push_1551722508_4859
vagrant snapshot restore jenkins-slave1 push_1551722512_3637
vagrant snapshot restore jenkins-slave2 push_1551722516_4328
vagrant snapshot restore support-tools push_1551722520_8112
vagrant snapshot restore nexus push_1551722524_772
vagrant snapshot restore sonarqube push_1551722528_8575
```

### Usando docsify
```
docsify ./docs //cria um template
docsify serve ./docs //inicia um server local
```

### Operando vmware fusion REST

#### Cria credenciais de acesso
```bash
vmrest -C
```

#### Infome usuário e senha
```
Username:
New password:
```
#### Inicia um serviço REST local
```
vmrest
```
O comando acima irá retornar uma URL, por exemplo:
```
VMware Fusion REST API
Copyright (C) 2015-2018 VMware Inc.
All Rights Reserved

vmrest 1.2.0 build-10952296
-
Using the VMware Fusion UI while API calls are in progress is not recommended and may yield unexpected results.
-
Serving HTTP on 127.0.0.1:8697
-
Press Ctrl+C to stop.
```
Acesse a página http://localhost:8697 (conforme URL acima)

Na página retornada no canto superior direito clique em **Authorization**
Informe o usuário e senha criado no passo `vmrest -C`.

![](/images/fig69-extra.png)
- Uma imagem similar será exibida permitindo consumir os serviços via REST API.

#### Keytool ops
##### list
```
keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts -storepass changeit
```
##### get cert
```
keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts -storepass changeit -alias 10.1.124.132
```
##### import new ca
```
keytool -import -trustcacerts -file ca.crt -alias 10.1.124.132 -keystore $JAVA_HOME/jre/lib/security/cacerts -storepass changeit
```
##### delete ca
```
keytool -delete -alias 10.1.124.131 -keystore $JAVA_HOME/jre/lib/security/cacerts -storepass changeit
```
### Acessando a API do SonarQube e buscando o resultado de uma análise
```
curl http://10.1.124.133:9000/api/qualitygates/project_status\?projectKey\=hello-world-greeting
```
