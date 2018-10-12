---
title: 使用Nexus管理maven仓库
date: '2018/8/22 19:26:46'
updated: '2018/9/25 22:27:03'
tags:
  - maven
  - Nexus
  - 配置
---
>[使用Nexus管理maven仓库，setting文件理解：](https://www.cnblogs.com/adolfmc/p/5069569.html)https://www.cnblogs.com/adolfmc/p/5069569.html

<!-- more -->

```xml
<!--  
xuze added by:添加了一些注释，利于新人理解  
Date:2011年7月18日  
-->  
<settings>  
  <!-- 配置镜像 -->  
  <mirrors>     
    <mirror>  
      <!-- 此镜像一般用来作为公司内部开发的版本快照，作为public-snapshots仓库的镜像地址 -->  
      <!-- 镜像的id,id用来区分不同的mirror元素。 -->   
      <id>nexus-public-snapshots</id>  
      <!-- 被镜像的服务器的id。例如，如果我们要设置了一个Maven中央仓库（http://repo1.maven.org/maven2）的镜像，  
        就需要将该元素设置成central。这必须和中央仓库的id “central”完全一致。 -->  
      <mirrorOf>public-snapshots</mirrorOf>  
      <!-- 该镜像的URL。 -->   
      <url>http://repos.d.xxx.com/nexus/content/groups/public-snapshots</url>  
    </mirror>  
      
    <mirror>  
      <!-- 此镜像一般用来作为公司第三方引用基础类库镜像，是所有仓库的镜像地址 -->  
      <id>nexus</id>  
      <!-- 为*表示为所有的仓库做镜像，有了这个配置，所有的构建都会包含public组，如果你想包含public-snapshots组，  
        你必须添加public-snapshots这个Profile，通过在命令行使用如下的 -P 标志:$ mvn -P public-snapshots clean install -->  
      <mirrorOf>*</mirrorOf>  
      <url>http://repos.d.xxx.com/nexus/content/groups/public</url>  
    </mirror>   
  </mirrors>  
    
  <!-- settings.xml中的profile元素是pom.xml中profile元素的裁剪版本。它包含了activation, repositories, pluginRepositories 和 properties元素。  
    这里的profile元素只包含这四个子元素是因为这里只关心构建系统这个整体（这正是settings.xml文件的角色定位），而非单独的项目对象模型设置。    
    如果一个settings中的profile被激活，它的值会覆盖任何其它定义在POM中或者profile.xml中的带有相同id的profile。 -->   
  <profiles>  
    <profile>  
      <id>development</id>  
      <!-- 仓库。仓库是Maven用来填充构建系统本地仓库所使用的一组远程项目。而Maven是从本地仓库中使用其插件和依赖。  
        不同的远程仓库可能含有不同的项目，而在某个激活的profile下，可能定义了一些仓库来搜索需要的发布版或快照版构件。有了Nexus，这些应该交由Nexus完成 -->  
      <repositories>  
        <repository>  
          <id>central</id>  
          <!-- 虚拟的URL形式,指向镜像的URL,因为所有的镜像都是用的是nexus，这里的central实际上指向的是http://repos.d.xxx.com/nexus/content/groups/public -->  
          <url>http://central</url>  
          <!-- 表示可以从这个仓库下载releases版本的构件-->  
          <releases><enabled>true</enabled></releases>  
          <!-- 表示可以从这个仓库下载snapshot版本的构件 -->  
          <snapshots><enabled>true</enabled></snapshots>  
        </repository>  
      </repositories>  
       
     <!-- 插件仓库。仓库是两种主要构件的家。第一种构件被用作其它构件的依赖。这是中央仓库中存储大部分构件类型。  
        另外一种构件类型是插件。Maven插件是一种特殊类型的构件。由于这个原因，插件仓库独立于其它仓库。  
        pluginRepositories元素的结构和repositories元素的结构类似。每个pluginRepository元素指定一个Maven可以用来寻找新插件的远程地址。 -->    
     <pluginRepositories>  
        <pluginRepository>  
          <id>central</id>  
          <url>http://central</url>  
          <releases><enabled>true</enabled></releases>  
          <snapshots><enabled>true</enabled></snapshots>  
        </pluginRepository>  
      </pluginRepositories>  
    </profile>  
      
    <profile>  
      <!--this profile will allow snapshots to be searched when activated-->  
      <id>public-snapshots</id>  
      <repositories>  
        <repository>  
          <id>public-snapshots</id>  
          <!-- 虚拟的URL形式,指向镜像的URL,这里指向的是http://repos.d.xxx.com/nexus/content/groups/public-snapshots -->  
          <url>http://public-snapshots</url>  
          <releases><enabled>false</enabled></releases>  
          <snapshots><enabled>true</enabled></snapshots>  
        </repository>  
      </repositories>  
     <pluginRepositories>  
        <pluginRepository>  
          <id>public-snapshots</id>  
          <url>http://public-snapshots</url>  
          <releases><enabled>false</enabled></releases>  
          <snapshots><enabled>true</enabled></snapshots>  
        </pluginRepository>  
      </pluginRepositories>  
    </profile>  
  </profiles>  
    
  <!-- 激活的Profile。activation元素并不是激活profile的唯一方式。settings.xml文件中的activeProfile元素可以包含profile的id，  
    任何在activeProfile中定义的profile id，不论环境设置如何，其对应的profile都会被激活。如果没有匹配的profile，则什么都不会发生。  
    profile也可以通过在命令行，使用-P标记和逗号分隔的列表来显式的激活（如，-P test）。  
    要了解在某个特定的构建中哪些profile会激活，可以使用maven-help-plugin（mvn help:active-profiles）。 -->    
  <activeProfiles>  
    <!-- 没有显示激活public-snapshots -->  
    <activeProfile>development</activeProfile>  
  </activeProfiles>  
  
<!-- 自定义本地仓库地址,其默认值为~/.m2/repository -->  
<localRepository>/data/maven-repository</localRepository>  
  
  <!-- 发布的服务器和密码，暂时未限制权限 -->  
   <servers>  
    <server>  
      <!-- 发布的位置在POM中配置，以ID为关联，有很多公用的信息需要配置在POM文件里，最佳实践是定义一个公司级别的root pom -->  
      <id>archiva.internal</id>  
      <username>maven</username>  
      <password>1q2w3e4r</password>  
    </server>  
    <server>  
      <id>archiva.snapshots</id>  
      <username>maven</username>  
      <password>1q2w3e4r</password>  
    </server>  
  </servers>  
</settings>  
```