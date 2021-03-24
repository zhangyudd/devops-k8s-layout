
安装的插件：

  Build With Parameters 多分支
  
  Git Parameter git多分支

  kubernetes k8s集群支持插件

  Email Extension 邮件扩展

  Role-based Authorization Strategy 权限管理

  Generic Webhook Trigger 仓库jenkins触发器

插件安装慢，无法连接解决方案：

    初始化完成后，修改里面得更新中心地址，删除pod，完成重启，安装插件

    cp hudson.model.UpdateCenter.xml  hudson.model.UpdateCenter.xml.bak

    vim hudson.model.UpdateCenter.xml
    可选地址：
      1. http://mirror.xmission.com/jenkins/updates/update-center.json
      2. http://mirror.esuni.jp/jenkins/updates/update-center.json
      3. https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
      4. https://updates.jenkins-zh.cn/update-center.json
