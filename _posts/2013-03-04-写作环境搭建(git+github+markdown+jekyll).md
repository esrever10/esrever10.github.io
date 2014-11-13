---

layout: post
categories: [Tools]
tags: [jekyll, github, git, markdown]

---

## 缘起

读了阳志平大神的一篇博文[理想的写作环境：git+github+markdown+jekyll](http://www.yangzhiping.com/tech/writing-space.html),不由感叹，这就是我想要的，于是就开始着手做这件事。在小站里也推荐了一下，然后发现，好多朋友早就已经搞起了，有的已经玩腻了，我果然又落伍了=。 =。这将是我第一篇用Markdown写的博文，当作练手~，真的很简单。

## 参考

这方面的好文章网上相当多，我挑几个自己用上的列出来：  

- git/github相关:
	+ [阳志平：Git与Github入门资料](http://www.yangzhiping.com/tech/git.html)  
	+ [阳志平：如何高效利用GitHub](http://www.yangzhiping.com/tech/github.html)  
- markdown相关：
	+ [Markdown 语法说明 (简体中文版)](http://wowubuntu.com/markdown/)
	+ [图灵社区 : 怎样使用Markdown](http://www.ituring.com.cn/article/23)
	+ [阳志平：Markdown写作浅谈](http://www.yangzhiping.com/tech/r-markdown-knitr.html)
- jekyall相关：
	+ [搭建一个免费的，无限流量的Blog----github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)
- 综述：
	+ [Github Pages极简教程 - 雁起平沙的网络日志](http://yanping.me/cn/blog/2012/03/18/github-pages-step-by-step/)
	+ [Play with Jekyll](http://blog.skydark.info/programming/2012/03/23/play-with-jekyll/)

说实话，这几篇看完了尤其是综述，就不用往下看了，以下作为我个人记录，只给出简单的过程以及排错经验，再无概念上的陈述，以备后用。

## 过程记录

### 以下过程在ubuntu 12.04下进行
1. apt-get安装rubygems之后，*gem install jekyll*安装jekyll，并用同样的gem命令安装directory_watcher、liquid、open4、maruku、classifier，rdiscount这几个包。Jekyll默认用maruku来解析markdown语言，你也可以用别的程序来解析，比如上面的rdiscount。

2. github上建立名为*username*.github.com的项目，其中*username*是你自己的github用户名。

3. *git clone*该项目至本地同名文件夹*username*.github.com。

4. 偷懒下载别人改好的jekyll模板框架，比较有名的是Octopress和Jekyll Bootstrap，另外[https://github.com/mojombo/jekyll/wiki/sites](https://github.com/mojombo/jekyll/wiki/sites)里面有很多可供参考的模板，我使用的是[kejinlu.github.com](https://github.com/kejinlu/kejinlu.github.com)。由于这些模板一般都是github上的，所以你可以直接*git clone*到本地，然后将其内容copy到你的*username*.github.com本地文件夹中, **注意一定要将他人的.git文件删掉**。

5. 接下来就是改动其内容，美其名曰hack，实际上我对前端代码完全看不懂，所以只做了最低级的修改，比如个人信息和文章神马的。改完后，push到github上，就算大功告成了，过个10分钟左右访问http://*username*.github.com，就可以看到你的blog雏形了。注意如果接到github发来的邮件告诉你发生错误，就说明没有正确配置，无法显示你的blog，我遇到过两个错误：

	+ >For information on troubleshooting Jekyll see https://help.github.com/articles/using-jekyll-with-pages#troubleshooting  

	这个错误一般是本地安装的jekyll或者ruby，rubygems等软件包版本不符合github的版本要求。你可以使用*gem list*命令查看本地的软件包版本号。以下是github上软件包的版本号要求。  

	        gem 'jekyll',     '=0.12.0'  
	        gem 'liquid',     '=2.4.1'  
	        gem 'redcarpet',  '=2.1.1'  
	        gem 'maruku',     '=0.6.0'  
	        gem 'rdiscount',  '=1.6.8'  
	        gem 'RedCloth',   '=4.2.9'
	
	
	+ >CNAME already taken: ......  

	这个错误需要把本地文件夹下的CNAME文件删掉，这个应该是别人定义自己自定义域名用的，如果你没有自定义域名，那就不需要。

6. 如果你的blog可以正确访问了，你就可以在_posts文件夹下添加.md文件作为你的博文，**注意命名格式一定要是yy-mm-dd-*title*.md**,*title*是你文章的标题。写完之后push到github上就可以在你的blog上显示了，其中每篇文章的开头都要指定一定的格式，如本文：

        ---
		layout: post
		categories: [Tools]
		tags: [jekyll, github, git, markdown]
		---

	*layout: post*指的是博文格式安装_layouts文件夹下的post.html指定的格式来显示。  
	*categories: [Tools]*指定博文分类。  
	*tags: [jekyll, github, git, markdown]*指定博文标签。

7. 如上所述，每次对博文做出改动都需要push到github上才能看到效果，那么如何不需要联网直接在本地调试呢，显然是可以的，因为你本地已经安装了jekyll（在第一步骤中），具体做法就是在本地*username*.github.com文件夹下运行命令，*jekyll serve*，在jekyll 0.12.0以前的命令好像是*jekyll --server*，这样就启动了jekyll本地端口的监听，当你访问*localhost:4000*时，就会看到你blog显示出来了。

## **注意事项**

1. 整个过程中最让我困扰的的是rubygems软件包版本的问题，用apt-get怎么也搞不定，可能是源的问题，最终我是从[rubygems.org](http://rubygems.org/)中搜索所有我想要的版本的包的，然后用*gem instll*安装包就可以了。

2. 在家里我用的ubuntu搭建的环境，在公司的时候我用windows想往github上push文章，但是发现都是乱码，这是因为我用的mysgit对中文支持不好，于是我按照[windows下git bash显示中文](http://blog.csdn.net/self001/article/details/7337182)的方法做了修改，并按照[git编辑器改用本机gvim](http://www.cuiguojie.com/msysgit_editor_gvim.html)的方法在mysgit下使用gvim或notepad++打开我的博文，保存为utf-8格式，这样再push就好用了。
