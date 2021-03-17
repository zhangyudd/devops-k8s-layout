
插件安装慢，无法连接解决方案：
  初始化完成后，修改里面得更新中心地址，删除pod，完成重启，安装插件
cp hudson.model.UpdateCenter.xml  hudson.model.UpdateCenter.xml.bak
vim hudson.model.UpdateCenter.xml

1. http://mirror.xmission.com/jenkins/updates/update-center.json

2. http://mirror.esuni.jp/jenkins/updates/update-center.json

3. https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

4. https://updates.jenkins-zh.cn/update-center.json
