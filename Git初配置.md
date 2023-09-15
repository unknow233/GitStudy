## 解决git@github.com: Permission denied (publickey).错误

进入git bash界面然后：
第一步，git config --global --list 验证邮箱与GitHub注册时输入的是否一致
![在这里插入图片描述](bin/20190521165825941.png)
第二步，通过git config --global [user.name](http://user.name/) “yourname”，git config --global user.email “email@email.com ”（这里得名字和邮箱都是注册github时用的)设置全局用户名和邮箱。

第三步，ssh-keygen -t rsa -C “这里换上你的邮箱”，一路回车，在出现选择时输入Y，再一路回车直到生成密钥。会在/Users/***/路径下生成一个.ssh文件夹，密钥就存储在其中。
![img](bin/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzY4OTQ2,size_16,color_FFFFFF,t_70.png)
第四步，到git仓库，添加秘钥，
![在这里插入图片描述](bin/20190521165333581.png)
![在这里插入图片描述](bin/20190521165414619.png)
![在这里插入图片描述](bin/20190521165436687.png)
![在这里插入图片描述](bin/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzY4OTQ2,size_16,color_FFFFFF,t_70-1694740197694-1.png)
点击Add SSH key

第五部，ssh -T [git@github.com](mailto:git@github.com) 测试一下通不通，通了显示如下
![在这里插入图片描述](bin/20190521165223872.png)
即可正常使用。
不通就是
ssh-agent -s
ssh-add ~/.ssh/id_rsa 操作这两步。