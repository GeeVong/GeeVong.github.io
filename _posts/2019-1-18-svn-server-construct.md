---
layout: post
title:  "svn服务器搭建遇到的坑"
date:   2019-1-18 
header-style: text
categories: 动动手
tags: 服务器搭建
---


### 具体教程，已有轮子
- [https://blog.csdn.net/kongxingxing/article/details/77192417](https://blog.csdn.net/kongxingxing/article/details/77192417)
    
### 遇一号坑
    
    ### This file controls the configuration of the svnserve daemon, if you
    ### use it to allow access to this repository.  (If you only allow
    ### access through http: and/or file: URLs, then this file is
    ### irrelevant.)
    
    ### Visit http://subversion.tigris.org/ for more information.
    
    [general]
    ### These options control access to the repository for unauthenticated
    ### and authenticated users.  Valid values are "write", "read",
    ### and "none".  The sample settings below are the defaults.
    anon-access = none
    auth-access = write
    ### The password-db option controls the location of the password
    ### database file.  Unless you specify a path starting with a /,
    ### the file's location is relative to the directory containing
    ### this configuration file.
    ### If SASL is enabled (see below), this file will NOT be used.
    ### Uncomment the line below to use the default password file.
    password-db = passwd
    ### The authz-db option controls the location of the authorization
    ### rules for path-based access control.  Unless you specify a path
    ### starting with a /, the file's location is relative to the the
    ### directory containing this file.  If you don't specify an
    ### authz-db, no path-based access control is done.
    ### Uncomment the line below to use the default authorization file.
    authz-db = authz
    ### This option specifies the authentication realm of the repository.
    ### If two repositories have the same authentication realm, they should
    ### have the same password database, and vice versa.  The default realm
    ### is repository's uuid.
    realm = /root/svn/repo1  （创库配置路径错误）
    
    [sasl]
    ### This option specifies whether you want to use the Cyrus SASL
    ### library for authentication. Default is false.
    ### This section will be ignored if svnserve is not built with Cyrus
    ### SASL support; to check, run 'svnserve --version' and look for a line
    ### reading 'Cyrus SASL authentication is available.'
    # use-sasl = true
    ### These options specify the desired strength of the security layer
    ### that you want SASL to provide. 0 means no encryption, 1 means
    ### integrity-checking only, values larger than 1 are correlated
    ### to the effective key length for encryption (e.g. 128 means 128-bit
    ### encryption). The values below are the defaults.
    # min-encryption = 0
    # max-encryption = 256










### 遇二号坑
    仓库里面项目的管理。由于一开始不理解svn真正的工作方式. 我直接拉取服务器节点，上传文件，
    但是发现在Linux端根本找不到对应文件。坑~，这是因为，我错认为服务器上的仓库也是一个类似于
    win上客户端svn的东西，但其实里面的数据存在并非想象的那样的，此处在做累述。
    
    
    win客户端可以通过 tortoisesvn repository browser 添加项目到仓库
    
    
    另外服务上也是可以向仓库中添加文件：
    svn add /root/workbench/my_skynet/my_workspace
    svn commit -m "commint a doc my_workspace"
    
    

### 遇三号坑
    编译skynet过程遇到jemalloc无法使用
    
    [root@vultr jemalloc]# ll
    total 0
    [root@vultr jemalloc]# 
    
    
    
    解决：[参考链接]
        make linux MALLOC_STATICLIB= SKYNET_DEFINES=-DNOUSE_JEMALLOC
        
     

[参考链接](https://manistein.github.io/blog/post/server/skynet/skynet%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E9%83%A8%E7%BD%B2/)