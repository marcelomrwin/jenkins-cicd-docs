#### Generate jenkins plugin list

https://192.168.56.110/script
```
Jenkins.instance.pluginManager.plugins.each{
  plugin ->
    println ("${plugin.getShortName()}:${plugin.getVersion()}")
}
```

#### Para gerar o password das contas utilizar
```
mkpasswd --method=sha-512
```

#### Gerar certificados para o Apache

**Step 1: Generate Private Key**
```
openssl genrsa –out ca.key 1024
```
**Step 2: Generate Certificate Request File**
```
openssl req –new –key ca.key –out ca.csr
```
**Step 3: Generate the Self Signed Certificate**
```
Openssl x509 –req –days 365 –in ca.csr –signkey ca.key –out ca.crt
```

#### [Jenkins Best Practices](https://wiki.jenkins.io/display/JENKINS/Jenkins+Best+Practices)
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

#### Vagrant operations
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

#### Usando docsify
```
docsify ./docs //cria um template
docsify serve ./docs //inicia um server local
```
