## 如何参与发布你的文章

默认你已经熟练的使用了 Github，知道git 命令。

**下载项目**

Fork [mulgore.github.io](https://github.com/mulgore/mulgore.github.io/)项目，并且将分支切换到 **hexo**。

	git clone git@github.com:mulgore/mulgore.github.io.git
	
	git checkout -b hexo origin/hexo

> 建议：Fork 此仓库后，请先从 hexo 分支上 git checkout -b hexo-your 一个新的 hexo-your 分支来书写文章，书写完成后再把 hexo-your 分支发 PR。

我们会在三天内进行 **Review**，通过之后会告知你发布时间，原则上你需要添加的任何信息都可以在文章的收尾得到添加（作者著名等）。

**安装依赖**

安装全局环境下的 **Hexo**

	npm install hexo-cli -g

然后在hexo分支下使用：

	npm install

**启动**

至此，你可以通过一个命令来预览：

	hexo s

关于 **hexo** 如何创建文章以及玩法，请参考 [https://hexo.io/](https://hexo.io/)

	hexo new [文章] // 文章名使用泛英文

## 版权声明

原则上需要在你文章末尾添加我们的微信公众号，地址是：[https://raw.githubusercontent.com/icepy/_posts/master/img/weixin.jpg](https://raw.githubusercontent.com/icepy/_posts/master/img/weixin.jpg)

![微信公众号：fed-talk](https://raw.githubusercontent.com/icepy/_posts/master/img/weixin.jpg)