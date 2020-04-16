###**什么是zsh？**
- **zsh类似bash也是一种shell.**

- zsh优点：
    - 兼容bash；
    - 智能拼写纠正；
    - 各种补全:路径,命令,命令参数；
- 如何安装zsh？
    
    Mac系统其实已经安装有zsh，但和大多数Linux类似，默认使用的是bash，不过我们只需简单进行配置即可更换
    ```angular2html
    执行cat /etc/shells可以查看系统已经安装的shell
  
  执行echo $SHELL可以查看用户当前使用的shell
     ```
  
- 执行如下命令，即可更改用户默认使用的shell为zsh(需要重新打开终端生效)
    
    ```angular2html
    执行zsh --version可以查看当前zsh的版本
    ```
  
- 如何配置zsh？

    - zsh的配置文件在~/.zshrc，刚安装后还没有这个文件。

    - 一种方式是我们可以手动创建这个文件，然后加入自己需要的配置（推荐）；
    
    - 另一种方式是我们可以从网上找别人已经配置好的文件，然后在这个模板上修改.
    
    - 这里推荐安装的是：oh-my-zsh；
    
    ```angular2html
      oh-my-zsh安装：https://github.com/robbyrussell/oh-my-zsh
    ```
  
- 还能做点啥呢？

    - zsh的主题
    
        - 下载agnoster主题，执行脚本安装：
    
        ```
            cd ~/Desktop/OpenSource
            
            git clone https://github.com/fcamblor/oh-my-zsh-agnoster-fcamblor.git
            
            cd oh-my-zsh-agnoster-fcamblor/
            
            ./install
    
        ```
  
        - 执行上面的命令会将主题拷贝到oh my zsh的themes中：
        
        ![avatar](http://upload-images.jianshu.io/upload_images/2411388-2f0643a027beb707.png)
       
        - 拷贝完成后，执行命令打开zshrc配置文件，将ZSH_THEME后面的字段改为agnoster。
        - 修改完成后按一下esc调出vi命令，输入:wq保存并退出vi模式。
        - source配置文件后,变样了
    - zsh的可选择、命令补全
    
        跟代码高亮的安装方式一样，这也是一个zsh的插件，叫做zsh-autosuggestion，用于命令建议和补全。
        
        ```cd ~/.oh-my-zsh/custom/plugins/
           
           git clone https://github.com/zsh-users/zsh-autosuggestions
           
           vi ~/.zshrc```
      
    - 安装高亮插件
    
        ```
           cd ~/.oh-my-zsh/custom/plugins/
           
           git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
           
           vi ~/.zshrc
        ```
        这时我们再次打开zshrc文件进行编辑。找到plugins，此时plugins中应该已经有了git，我们需要把高亮插件也加上：
        
        ![avatar](http://upload-images.jianshu.io/upload_images/2411388-57a605b95bc03af4.png)
        
        请务必保证插件顺序，zsh-syntax-highlighting必须在最后一个。
        
        
### 大功告成

        
        
    
