---
layout: post
title: Jenkins module to Puppet
date: 2013-12-01 19:49:05.000000000 +02:00
categories:
- Linux
- Linux centralized management
tags:
- Debian
- Git
- Jenkins
- Linux
- Module
- Puppet
- Ubuntu
- Ubuntu 12.04
- Xubuntu
- Xubuntu 12.04
permalink: "/2013/jenkins-module-to-puppet/"
---
**If you haven't seen my previous tutorials you should see them:  
[How to install Puppet](/2013/how-to-install-puppet/), [Hello World module using template to Puppet](/2013/template-hello-world-module-to-puppet/),  
[Installing Apache and PHP with Puppet module](/2013/installing-apache-and-php-with-puppet-module/), [Installing Puppet master and slaves](/2013/installing-puppet-master-and-slaves/),  
[Parametrized Class with Puppet](/2013/parametrized-class-with-puppet/), [Define Types example in Puppet](/2013/define-types-example-in-puppet/), [How to get shared Git repository to server](/2013/how-to-get-shared-git-repository-to-server/).  
**

I'm using Xubuntu 12.04.03.

In this tutorial I'm creating module that:  
- Installs Git and Jenkins ( Service makes sure Jenkins is up and running )  
- Adds 7 plugins that Jenkins needs to get Git working in Jenkins ( Service restarts Jenkins if some plugin is added )

I install:  
- Git 1.7.9.5  
- Jenkins 1.424.6  
Plugins  
- credentials.hpi 1.9.4  
- git.hpi 2.0  
- github.hpi 1.8  
- ssh-credentials.hpi 1.6  
- git-client.hpi 1.4.6  
- github-api.hpi 1.44  
- scm-api.hpi 0.2

## Jenkins module

Install Puppet, create folders and init.pp.

{% highlight shell %}
$ sudo apt-get update && sudo apt-get -y install puppet
$ mkdir -p modules/jenkins/manifests/
$ nano modules/jenkins/manifests/init.pp
{% endhighlight %}

Init.pp is using plugins.pp define type file to adding all plugins files.

{% highlight shell %}
class jenkins {
	$pathvariable = "/var/lib/jenkins"
        $sourcevariable = "puppet:///modules/jenkins"

        package {'git':
                ensure => present,
        }

        package {'jenkins':
                ensure => present,
        }

	service {'jenkins':
                ensure => running,
                enable => true,
                require => Package["jenkins"],
        }

	file{[$pathvariable, "${pathvariable}/plugins"]:
                ensure => directory,
                owner => "jenkins",
                group => "jenkins",
                mode => 755,
                require => Package["jenkins"],
                notify => Service["jenkins"],
        }

        jenkins::plugins {"${jenkins::pathvariable}/plugins/credentials.hpi":
                pluginpath => "${sourcevariable}/credentials.hpi",
        }

        jenkins::plugins {"${jenkins::pathvariable}/plugins/git-client.hpi":
                pluginpath => "${sourcevariable}/git-client.hpi",
        }

        jenkins::plugins {"${jenkins::pathvariable}/plugins/git.hpi":
                pluginpath => "${sourcevariable}/git.hpi",
        }

        jenkins::plugins {"${jenkins::pathvariable}/plugins/github-api.hpi":
                pluginpath => "${sourcevariable}/github-api.hpi",
        }

        jenkins::plugins {"${jenkins::pathvariable}/plugins/github.hpi":
                pluginpath => "${sourcevariable}/github.hpi",
        }

        jenkins::plugins {"${jenkins::pathvariable}/plugins/scm-api.hpi":
                pluginpath => "${sourcevariable}/scm-api.hpi",
        }

        jenkins::plugins {"${jenkins::pathvariable}/plugins/ssh-credentials.hpi":
                pluginpath => "${sourcevariable}/ssh-credentials.hpi",
        }
}
{% endhighlight %}

Plugins.pp determines plugins .hpi files configures.

{% highlight shell %}
$ nano modules/jenkins/manifests/plugins.pp

define jenkins::plugins ($pluginpath) {
                file {"$title":
                        ensure  => file,
                        source => $pluginpath,
                        owner => "jenkins",
                        group => "jenkins",
                        mode => 644,
                        notify => Service["jenkins"],
                        require => File["${jenkins::pathvariable}/plugins"],
                }
}
{% endhighlight %}

Create files folder where you add .hpi files what Jenkins needs to have to get plugins working. Module copies these files to /var/lib/jenkins/plugins/

{% highlight shell %}
$ mkdir -p modules/jenkins/files/
$ cd modules/jenkins/files/
$ wget https://updates.jenkins-ci.org/download/plugins/credentials/1.9.4/credentials.hpi
$ wget https://updates.jenkins-ci.org/download/plugins/git-client/1.4.6/git-client.hpi
$ wget https://updates.jenkins-ci.org/download/plugins/git/2.0/git.hpi
$ wget https://updates.jenkins-ci.org/download/plugins/github-api/1.44/github-api.hpi
$ wget https://updates.jenkins-ci.org/download/plugins/github/1.8/github.hpi
$ wget https://updates.jenkins-ci.org/download/plugins/ssh-credentials/1.6/ssh-credentials.hpi
$ wget https://updates.jenkins-ci.org/download/plugins/scm-api/0.2/scm-api.hpi
$ cd ../../..
{% endhighlight %}

Apply module. This could take couple minutes because Jenkins installation could take a while.

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"jenkins":}'
{% endhighlight %}

Something like this should terminal print if module works

{% highlight shell %}
notice: /Stage[main]/Jenkins/Package[git]/ensure: ensure changed 'purged' to 'present'
notice: /Stage[main]/Jenkins/Package[jenkins]/ensure: ensure changed 'purged' to 'present'
notice: /Stage[main]/Jenkins/Jenkins::Plugins[/var/lib/jenkins/plugins/github.hpi]/File[/var/lib/jenkins/plugins/github.hpi]/ensure: defined content as '{md5}9b5f1bb17a78e77c390528b25b84071a'
notice: /Stage[main]/Jenkins/Jenkins::Plugins[/var/lib/jenkins/plugins/github-api.hpi]/File[/var/lib/jenkins/plugins/github-api.hpi]/ensure: defined content as '{md5}7969f66ebad949af4aac8836c8e83a8a'
notice: /Stage[main]/Jenkins/Jenkins::Plugins[/var/lib/jenkins/plugins/git-client.hpi]/File[/var/lib/jenkins/plugins/git-client.hpi]/ensure: defined content as '{md5}3d4001519e93206b3bbf7f932bcce41c'
notice: /Stage[main]/Jenkins/Jenkins::Plugins[/var/lib/jenkins/plugins/ssh-credentials.hpi]/File[/var/lib/jenkins/plugins/ssh-credentials.hpi]/ensure: defined content as '{md5}d708cebf8722261fe680d331f9096ace'
notice: /Stage[main]/Jenkins/Jenkins::Plugins[/var/lib/jenkins/plugins/credentials.hpi]/File[/var/lib/jenkins/plugins/credentials.hpi]/ensure: defined content as '{md5}1dc8897de4880f3d3f634b345ead856c'
notice: /Stage[main]/Jenkins/Jenkins::Plugins[/var/lib/jenkins/plugins/git.hpi]/File[/var/lib/jenkins/plugins/git.hpi]/ensure: defined content as '{md5}7c009e0c0fb588e945edd1cc9f313330'
notice: /Stage[main]/Jenkins/Jenkins::Plugins[/var/lib/jenkins/plugins/scm-api.hpi]/File[/var/lib/jenkins/plugins/scm-api.hpi]/ensure: defined content as '{md5}9574c07bf6bfd02a57b451145c870f0e'
notice: /Stage[main]/Jenkins/Service[jenkins]/enable: enable changed 'false' to 'true'
notice: /Stage[main]/Jenkins/Service[jenkins]: Triggered 'refresh' from 7 events
notice: Finished catalog run in 169.58 seconds
{% endhighlight %}

Let's see if Jenkins is really working

{% highlight shell %}
$ firefox localhost:8080
{% endhighlight %}

It works!  
![puppetjenkins1](/2013/12/puppetjenkins1.png)  

Let's check if plugins really installed. Push manage Jenkins  
![puppetjenkins2](/2013/12/puppetjenkins2.png)  
And select manage plugins  
![puppetjenkins3](/2013/12/puppetjenkins3.png)  
Choose installed plugins. We are installed 7 plugins. All is there what we need to get Git plugin working in Jenkins  
![puppetjenkins4](/2013/12/puppetjenkins4.png)  

## Test project to Jenkins

Let's create testproject to Jenkins.  
Default folder to Git repository I use /home/soivishare/repository/helloGit.git  
If you wanna use this here is tutorial how to get it working:  
[How to get shared Git repository to server](/2013/how-to-get-shared-git-repository-to-server/)

But you can easily change it giving attributes to parameterized class.

Create new module and init.pp.

{% highlight shell %}
$ mkdir -p modules/jenkinstestjob/manifests/
$ nano modules/jenkinstestjob/manifests/init.pp
{% endhighlight %}

Grpath is variable which determines path of the repository. This module checks if config.xml exists do not replace it.

{% highlight shell %}
class jenkinstestjob($grpath = "/home/soivishare/repository/helloGit.git") {
        $pathvariable = "/var/lib/jenkins/jobs/jenkinstestjob"

        file { $pathvariable:
                ensure => directory,
                group  => "jenkins",
                mode   => 755,
                owner  => "jenkins",
                notify => Service["jenkins"],
        }

        file { "${pathvariable}/config.xml":
                ensure  => present,
                replace => no,
                content => template('jenkinstestjob/config.xml.erb'),
                group   => "jenkins",
                mode    => 644,
                owner   => "jenkins",
                require => File[$pathvariable],
                notify => Service["jenkins"],
        }

        service {'jenkins':
                ensure => running,
                enable => true,
        }
}
{% endhighlight %}

Create template config.xml.erb.

{% highlight shell %}
$ mkdir -p modules/jenkinstestjob/templates/
$ nano modules/jenkinstestjob/templates/config.xml.erb
{% endhighlight %}

This is copy of normal config.xml in jenkins jobs, but url variable is now <%= grpath %>.  
So the path of the repository is easy to change when running module the first time.

{% highlight shell %}
<?xml version='1.0' encoding='UTF-8'?>
<project>
  <actions/>
  <description></description>
  <keepDependencies>false</keepDependencies>
  <properties/>
  <scm class="hudson.plugins.git.GitSCM">
    <configVersion>2</configVersion>
    <userRemoteConfigs>
      <hudson.plugins.git.UserRemoteConfig>
        <url><%= grpath %></url>
      </hudson.plugins.git.UserRemoteConfig>
    </userRemoteConfigs>
    <branches>
      <hudson.plugins.git.BranchSpec>
        <name>*/master</name>
      </hudson.plugins.git.BranchSpec>
    </branches>
    <doGenerateSubmoduleConfigurations>false</doGenerateSubmoduleConfigurations>
    <submoduleCfg class="list"/>
    <extensions/>
  </scm>
  <canRoam>true</canRoam>
  <disabled>false</disabled>
  <blockBuildWhenDownstreamBuilding>false</blockBuildWhenDownstreamBuilding>
  <blockBuildWhenUpstreamBuilding>false</blockBuildWhenUpstreamBuilding>
  <triggers class="vector"/>
  <concurrentBuild>false</concurrentBuild>
  <builders/>
  <publishers/>
  <buildWrappers/>
</project>
{% endhighlight %}

Now you can run module and use default path /home/soivishare/repository/helloGit.git

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"jenkinstestjob":}'
{% endhighlight %}

Or change repository path example our TiPi project.

{% highlight shell %}
$ sudo puppet apply --modulepath modules/ -e 'class {"jenkinstestjob": grpath => "https://github.com/Scionar/TiPi.git"}'
{% endhighlight %}

New job is added to jenkins. Push jobs name.  
![puppetjenkins5](/2013/12/puppetjenkins5.png)  

Choose configure  
![puppetjenkins6](/2013/12/puppetjenkins6.png)  

In here you can see what is repositorys path and you can make changes to project if you like  
![puppetjenkins7](/2013/12/puppetjenkins7.png)  

Push Build Now. Project is building and after that you can see in workspace your git project.  
![puppetjenkins8](/2013/12/puppetjenkins8.png)  

Your folder tree should look like this.

{% highlight shell %}
modules/
├── jenkins
│   ├── files
│   │   ├── credentials.hpi
│   │   ├── git-client.hpi
│   │   ├── git.hpi
│   │   ├── github-api.hpi
│   │   ├── github.hpi
│   │   ├── scm-api.hpi
│   │   └── ssh-credentials.hpi
│   └── manifests
│       ├── init.pp
│       └── plugins.pp
└── jenkinstestjob
    ├── manifests
    │   └── init.pp
    └── templates
        └── config.xml.erb
{% endhighlight %}

Now you have Jenkins module that installs Git plugin. You have testproject what uses Jenkins and Git plugin.

This post is part of [course](http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013)