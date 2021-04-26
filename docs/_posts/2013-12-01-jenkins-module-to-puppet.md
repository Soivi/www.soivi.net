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
<p><strong>If you haven't seen my previous tutorials you should see them:<br />
<a href="http://soivi.net/2013/how-to-install-puppet/">How to install Puppet</a>, <a href="http://soivi.net/2013/template-hello-world-module-to-puppet/">Hello World module using template to Puppet</a>,<br />
<a href="http://soivi.net/2013/installing-apache-and-php-with-puppet-module/">Installing Apache and PHP with Puppet module</a>, <a href="http://soivi.net/2013/installing-puppet-master-and-slaves/">Installing Puppet master and slaves</a>,<br />
 <a href="http://soivi.net/2013/parametrized-class-with-puppet/">Parametrized Class with Puppet</a>,<a href="http://soivi.net/2013/define-types-example-in-puppet/"> Define Types example in Puppet</a>, <a href="http://soivi.net/2013/how-to-get-shared-git-repository-to-server/">How to get shared Git repository to server</a>.<br />
</strong></p>
<p>I'm using Xubuntu 12.04.03.</p>
<p>In this tutorial I'm creating module that:<br />
- Installs Git and Jenkins ( Service makes sure Jenkins is up and running )<br />
- Adds 7 plugins that Jenkins needs to get Git working in Jenkins ( Service restarts Jenkins if some plugin is added )</p>
<p>I install:<br />
- Git 1.7.9.5<br />
- Jenkins 1.424.6<br />
Plugins<br />
- credentials.hpi 1.9.4<br />
- git.hpi 2.0<br />
- github.hpi 1.8<br />
- ssh-credentials.hpi 1.6<br />
- git-client.hpi 1.4.6<br />
- github-api.hpi 1.44<br />
- scm-api.hpi 0.2 </p>
<h2>Jenkins module</h2>
<p>Install Puppet, create folders and init.pp.</p>
<pre>
$ sudo apt-get update && sudo apt-get -y install puppet
$ mkdir -p modules/jenkins/manifests/
$ nano modules/jenkins/manifests/init.pp
</pre>
<p>Init.pp is using plugins.pp define type file to adding all plugins files.</p>
<pre>
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
}</pre>
<p>Plugins.pp determines plugins .hpi files configures.</p>
<pre>
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
</pre>
<p>Create files folder where you add .hpi files what Jenkins needs to have to get plugins working. Module copies these files to /var/lib/jenkins/plugins/</p>
<pre>
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
</pre>
<p>Apply module. This could take couple minutes because Jenkins installation could take a while.</p>
<pre>
$ sudo puppet apply --modulepath modules/ -e 'class {"jenkins":}'
</pre>
<p>Something like this should terminal print if module works</p>
<pre>notice: /Stage[main]/Jenkins/Package[git]/ensure: ensure changed 'purged' to 'present'
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
notice: Finished catalog run in 169.58 seconds</pre>
<p>Let's see if Jenkins is really working</p>
<pre>
$ firefox localhost:8080
</pre>
<p>It works!<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins1.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins1.png" alt="puppetjenkins1" width="1363" height="569" class="alignnone size-full wp-image-371" /></a></p>
<p>Let's check if plugins really installed. Push manage Jenkins<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins2.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins2.png" alt="puppetjenkins2" width="132" height="202" class="alignnone size-full wp-image-372" /></a><br />
And select manage plugins<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins3.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins3.png" alt="puppetjenkins3" width="518" height="53" class="alignnone size-full wp-image-373" /></a><br />
Choose installed plugins. We are installed 7 plugins. All is there what we need to get Git plugin working in Jenkins<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins4.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins4.png" alt="puppetjenkins4" width="868" height="322" class="alignnone size-full wp-image-374" /></a></p>
<h2>Test project to Jenkins</h2>
<p>Let's create testproject to Jenkins.<br />
Default folder to Git repository I use /home/soivishare/repository/helloGit.git<br />
If you wanna use this here is tutorial how to get it working:<br />
<a href="http://soivi.net/2013/how-to-get-shared-git-repository-to-server/">How to get shared Git repository to server</a></p>
<p>But you can easily change it giving attributes to parameterized class.</p>
<p>Create new module and init.pp.</p>
<pre>
$ mkdir -p modules/jenkinstestjob/manifests/
$ nano modules/jenkinstestjob/manifests/init.pp
</pre>
<p>Grpath is variable which determines path of the repository. This module checks if config.xml exists do not replace it.</p>
<pre>
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
</pre>
<p>Create template config.xml.erb. </p>
<pre>
$ mkdir -p modules/jenkinstestjob/templates/
$ nano modules/jenkinstestjob/templates/config.xml.erb
</pre>
<p>This is copy of normal config.xml in jenkins jobs, but url variable is now &lt;%= grpath %&gt;.<br />
So the path of the repository is easy to change when running module the first time.</p>
<pre>
&lt;?xml version=&#39;1.0&#39; encoding=&#39;UTF-8&#39;?&gt;
&lt;project&gt;
  &lt;actions/&gt;
  &lt;description&gt;&lt;/description&gt;
  &lt;keepDependencies&gt;false&lt;/keepDependencies&gt;
  &lt;properties/&gt;
  &lt;scm class=&quot;hudson.plugins.git.GitSCM&quot;&gt;
    &lt;configVersion&gt;2&lt;/configVersion&gt;
    &lt;userRemoteConfigs&gt;
      &lt;hudson.plugins.git.UserRemoteConfig&gt;
        &lt;url&gt;&lt;%= grpath %&gt;&lt;/url&gt;
      &lt;/hudson.plugins.git.UserRemoteConfig&gt;
    &lt;/userRemoteConfigs&gt;
    &lt;branches&gt;
      &lt;hudson.plugins.git.BranchSpec&gt;
        &lt;name&gt;*/master&lt;/name&gt;
      &lt;/hudson.plugins.git.BranchSpec&gt;
    &lt;/branches&gt;
    &lt;doGenerateSubmoduleConfigurations&gt;false&lt;/doGenerateSubmoduleConfigurations&gt;
    &lt;submoduleCfg class=&quot;list&quot;/&gt;
    &lt;extensions/&gt;
  &lt;/scm&gt;
  &lt;canRoam&gt;true&lt;/canRoam&gt;
  &lt;disabled&gt;false&lt;/disabled&gt;
  &lt;blockBuildWhenDownstreamBuilding&gt;false&lt;/blockBuildWhenDownstreamBuilding&gt;
  &lt;blockBuildWhenUpstreamBuilding&gt;false&lt;/blockBuildWhenUpstreamBuilding&gt;
  &lt;triggers class=&quot;vector&quot;/&gt;
  &lt;concurrentBuild&gt;false&lt;/concurrentBuild&gt;
  &lt;builders/&gt;
  &lt;publishers/&gt;
  &lt;buildWrappers/&gt;
&lt;/project&gt;
</pre>
<p>Now you can run module and use default path /home/soivishare/repository/helloGit.git</p>
<pre>
$ sudo puppet apply --modulepath modules/ -e 'class {"jenkinstestjob":}'
</pre>
<p>Or change repository path example our TiPi project.</p>
<pre>
$ sudo puppet apply --modulepath modules/ -e 'class {"jenkinstestjob": grpath => "https://github.com/Scionar/TiPi.git"}'
</pre>
<p>New job is added to jenkins. Push jobs name.<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins5.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins5.png" alt="puppetjenkins5" width="806" height="73" class="alignnone size-full wp-image-415" /></a></p>
<p>Choose configure<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins6.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins6.png" alt="puppetjenkins6" width="199" height="272" class="alignnone size-medium wp-image-416" /></a></p>
<p>In here you can see what is repositorys path and you can make changes to project if you like<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins7.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins7.png" alt="puppetjenkins7" width="300" height="95" class="alignnone size-medium wp-image-417" /></a></p>
<p>Push Build Now. Project is building and after that you can see in workspace your git project.<br />
<a href="http://soivi.net/wp-content/uploads/2013/12/puppetjenkins8.png"><img src="{{ site.baseurl }}/assets/2013/12/puppetjenkins8.png" alt="puppetjenkins8" width="490" height="307" class="alignnone size-full wp-image-418" /></a></p>
<p>Your folder tree should look like this.</p>
<pre>
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
</pre>
<p>Now you have Jenkins module that installs Git plugin. You have testproject what uses Jenkins and Git plugin.</p>
<p>This post is part of <a href="http://terokarvinen.com/2013/aikataulu-%E2%80%93-linuxin-keskitetty-hallinta-%E2%80%93-ict4tn011-4-syksylla-2013">course</a></p>
