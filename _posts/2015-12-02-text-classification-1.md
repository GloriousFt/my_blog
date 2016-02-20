---
layout: post
title: "文本分类（一）爬虫抓取文章生成语料库"
date: 2015-12-02 02:04:40.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- DataMining
- Scrapy
meta:
  _edit_last: '1'
---
<p>文本分类是数据挖掘中常用到的，之前学过只做过一些邮件分类的东西，这次准备系统学习一下，弄个10000+的语料库来个分类。</p>
<p><strong>文本分类的主要流程</strong></p>
<p>文本分类主要需要完成以下几步：</p>
<ul>
<li>获取需要分析的文档形成语料库</li>
<li>对语料库进行预处理以便进行计算处理</li>
<li>文本特征提取及表达</li>
<li>用训练集对分类器进行训练</li>
<li>用测试集队分类器进行测试</li>
<li>通过混淆矩阵等统计信息查看分类效果</li>
</ul>
<!--more-->
<p>个人认为，其实以上最麻烦的一步就是数据预处理和文本特征提取。因为现在文本分类技术已经相对来说比较成熟，许多已有的一些挖掘工具已经提供了很好的API来进行分类器的操作，比如<a title="R官方网站" href="http://www.r-project.org/" target="_blank">R</a>，Python的<a title="NLTK自然语言处理" href="http://www.nltk.org" target="_blank">NLTK</a>，<a title="Weka" href="http://weka.wikispaces.com" target="_blank">Weka</a>等等。而数据预处理要做很多（以后会更新到这里），包括分词，去词，代入用户字典等等。文本特征提取还要看用哪种方法，是词频，还是文档频率，还是一些其他的如互信息等等。表达是用词袋还是主题模型，这些工作处理起来都是比较耗时的。当然做起来是比听起来更简单的，接下来就一点一点来搞懂，文本分类具体都要做些什么工作。</p>
<p>第一篇就来讲一下语料库是如何获取的。</p>
<p>语料库就是要进行挖掘处理的文档集，它可以是一个数据库中的部分数据，也可以是一些统计软件格式的文件，如微软Excel文件，还可以是普通的text文本汇总。如果是专做数据挖掘的公司，那么它的语料库来源可能就是用户的一些数据，这里要做的，是想用爬虫抓取一些新闻网上的文章，保存下来做一个语料库。</p>
<p>爬虫当然是用python写最好，它提供了许多库来做爬虫，urllib2等。那爬虫工作原理是什么呢？</p>
<p>Spider是网络数据抓取非常形象的名字，蜘蛛就是用一张网来捕获猎物，Spider也一样，在网络上通过深度优先遍历或广度优先遍历的方法，不停地在网络上抓取信息。 浏览器获取DOM文档，然后解析显示给用户看，Spider获取DOM文档，通过程序员编写指定的获取HTML标签方法，将相应的标签中的内容获取整合，最后保存。所以简单来说，Spider就是一个不停地获取DOM，解析标签并保存数据的一个过程。数据可以是文档，可以是视频，也可以是图片。现在有很多公司专门做爬虫抓取来丰富自己数据库，简单方便。</p>
<p>这里介绍一个Python爬虫工具，<a title="Scrapy中文教程" href="http://scrapy-chs.readthedocs.org/zh_CN/0.24/intro/tutorial.html" target="_blank">Scrapy</a>！Quote Here：<span style="color: #404040;">Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 可以应用在包括数据挖掘，信息处理或存储历史数据等一系列的程序中。上面Scrapy中文教程里写得非常详细，官方Documents永远是最好的学习工具，这里就不再重复了，直接开始如何抓取Sina和Tencent的新闻，Let‘s go！建议先自行查看一下官方Documents。</span></p>
<p>首先安装python2.7或以上版本，然后用pip安装scrapy：pip install Scrapy，如果有任何缺失导致安装失败，可以看一下这个链接的内容：<a title="安装Scrapy" href="http://scrapy-chs.readthedocs.org/zh_CN/0.24/intro/install.html" target="_blank">安装Scrapy</a>。</p>
<p>之后在目标文件夹创建Scrapy项目，并且生成一个Spider。这里就把项目名字叫做qqtechtext。我们要把腾讯科技网（tech.qq.com）的新闻搞下来。</p>
<p><code><br />
    scrapy startproject qqtechtext<br />
</code></p>
<p>生成后能够看到项目的目录结构，虽然官方介绍过，但这里还是明白清楚地说一下目录内的文件都是干嘛用的，首先第一个，项目文件夹下的items.py，这个是定义你想保存的结构化数据是什么样的，比如说，我们想保存的是网站上的文章，那么我们就保存文章名字和文章内容就可以了。我们指定title对应标题，text对应文本，那么这里就可以定义item为：</p>
<p><code lang="python"><br />
# -*- coding: utf-8 -*-<br />
from scrapy.item import Item, Field<br />
class QqtechtextItem(Item):<br />
    title = Field()<br />
    text = Field()<br />
</code><br />
第二个pipelines.py是说，获取到item之后，还要进行什么后续处理的，比如字符处理，还是保存，因为代码较短，所以这里就把数据保存直接写到Spider里面了，非常不推崇这样做（我懒）。</p>
<p>第三个setting.py，这个是配置文件，比如加入items的pipelines啊，还有开启cookie啊等等，对于我们要做的新闻抓取来说，这个暂时还用不到。</p>
<p>好，下面是最关键的spiders文件夹，我们编写的spiders都是写在这里的。从类定义开始，一点一点往下看，首先定义爬虫的名字，然后设置允许的域名域，这是防止spider走跑偏的，万一跑的太远冲到别的网站就不好了。还要设置一个起始的url，这是给spider一个起点的，从哪个url开始。<br />
<code lang="python"><br />
    name = "qqtech"<br />
    allowed_domains = ["tech.qq.com"]<br />
    start_urls = (<br />
        'http://tech.qq.com/',<br />
        'http://tech.qq.com/it.htm',<br />
        # tencent tech news<br />
    )<br />
</code><br />
因为我们要编写的spider是要进行循环递归抓取的，所以这里要导入一个rules规则，判断什么样的链接我们可以让spider继续进行，什么样的链接我们看到就走，理都不理。<br />
<code lang="python"><br />
rules = (Rule(LinkExtractor(allow=('/a/\d{,8}/\d{,6}.htm'),), callback='parse_item',follow=True),)<br />
</code><br />
这个就是我定义的链接规则，不要急，我们一点一点来看每个参数的意义，第一个，LinkExtrator，这个是链接提取的一个方法，它有很多参数，点<a title="LinkExtractor" href="http://scrapy-chs.readthedocs.org/zh_CN/0.24/topics/link-extractors.html" target="_blank">这里</a>查看详情，其中allow的意思是，通过正则表达式，来匹配符合链接要求的所有链接，如果符合则调用后面callback的函数。follow是true，就是说当爬虫获取到符合规则的新链接时，将以这个新链接为新起点，重新寻找新链接里面的所有链接再来进行链接匹配。如果设置成false，那么就只找寻最开始页面的链接。</p>
<p>接下来就该定义我们的回调函数parse_item了，我们可以先将parse_item里面只写print 'go'，然后测试看看整个爬虫能不能工作。<br />
<code lang="python"><br />
# -*- coding: utf-8 -*-<br />
import scrapy<br />
import os<br />
import codecs<br />
import scrapy<br />
from qqtechtext.items import QqtechtextItem<br />
from scrapy.selector import Selector<br />
from scrapy.spiders import CrawlSpider, Rule<br />
from scrapy.linkextractors import LinkExtractor</p>
<p>class QqtechSpider(CrawlSpider):<br />
    name = "qqtech"<br />
    allowed_domains = ["tech.qq.com"]<br />
    start_urls = (<br />
        'http://tech.qq.com/',<br />
        'http://tech.qq.com/it.htm',<br />
        # tencent tech news<br />
    )<br />
    rules = (Rule(LinkExtractor(allow=('/a/\d{,8}/\d{,6}.htm'),), callback='parse_item',follow=True),)<br />
    def parse_item(self, response):<br />
    	print 'go'<br />
        return item<br />
</code><br />
运行一下试试吧，在项目文件夹下，输入：<br />
<code><br />
    scrapy crawl qqtech<br />
</code><br />
go的停不下来！有没有！</p>
<p>说明rules的正则匹配成功了，那么我们到底找的是哪些文章，用chrome打开qq.tech.com，再找一片文章进去，查看它的url，基本都是http://tech.qq.com/a/日期/文章编号.htm。那好，我们就要找这样的文章，所以我们就匹配'/a/\d{,8}/\d{,6}.htm'这样的表达式就好，关于正则表达式，可以看我之前另一篇文章，不过这里还是推荐别人的一个<a title="正则表达式教程" href="https://msdn.microsoft.com/zh-cn/library/ae5bf541(VS.80).aspx" target="_blank">正则表达式</a>学习的文章吧，毕竟写得没人家好（人家是微软）。Anyway，这里的表达式含义就是，先匹配a字符，然后匹配8个数字，再匹配6个数字，最后加上htm。我们能够在tech.qq.com上找到成顿的这种url的技术文章。下面我们要做的就是人工解析页面。</p>
<p>用chrome开发者工具打开<a title="机器人热潮将引发全球失业危机？" href="http://tech.qq.com/a/20151202/009984.htm" target="_blank">http://tech.qq.com/a/20151202/009984.htm</a>这个页面，用它做小白鼠。</p>
<p>找到它的title－“机器人热潮将引发全球失业危机？”，这个我们要了！查看它的html代码，发现它在：<br />
<code lang="html"></p>
<div id="C-Main-Article-QQ" class="mod-left">
<div class="hd">
<h1>机器人热潮将引发全球失业危机？</h1>
</div>
</div>
<p></code><br />
那我们就要用css或者xpath选择器选到它，然后extract它的text。选择器又是什么呢？做过前端开发的都知道，用js或jquery时，选择某一个标签时是通过选择器来实现的，那么这里也是一样：<br />
<code lang="python"><br />
css('div.hd').xpath('h1/text()').extract()<br />
</code><br />
这个就是先通过css的方法，找到拥有“hd”class的div标签，然后再通过xpath的方法，获取里面的h1标签，再获取text，然后提取。上面的表达式返回值就是我们要的那段title。关于选择器的学习，点<a title="selector选择器" href="http://scrapy-chs.readthedocs.org/zh_CN/0.24/topics/selectors.html" target="_blank">这里</a>。</p>
<p>会了选择一个，就会了选择千千万个。再查看文章内容的标签，ppppppppp全是p标签，那就一个for循环解析出来好了：<br />
<code lang="python"><br />
for paragraph in sel.css('div.bd').css('div[id="Cnt-Main-Article-QQ"]').xpath('p/text()'):<br />
    newtext = paragraph.extract()<br />
    text = text + newtext.strip()</code><br />
原理都是一样的，最后的文章就存在text变量里面。</p>
<p>到这里基本就大功告成了，scrapy最基本的功能基本都接触到了，我们只需要再保存下text的就好了。最后把回调函数的代码贴出来：<br />
<code lang="python"><br />
def parse_item(self, response):<br />
    	sel = Selector(response)<br />
    	item = QqtechtextItem()<br />
        item['title'] = sel.css('div.hd').xpath('h1/text()').extract()<br />
        text = ''<br />
        for paragraph in sel.css('div.bd').css('div[id="Cnt-Main-Article-QQ"]').xpath('p/text()'):<br />
            newtext = paragraph.extract()<br />
            text = text + newtext.strip()<br />
        item['text'] = text<br />
        if(len(text) < 200):<br />
        	return item<br />
        # print repr(item).decode("unicode-escape") + '\n'</p>
<p>        filename = ''.join(item['title']) + '.txt'</p>
<p>        # os.path API invalid, just hack here<br />
        absDirectory = '../qqtechtext/tech/'<br />
        p = os.path.abspath(absDirectory + filename)<br />
        with codecs.open(p, 'w', 'utf-8') as f:<br />
            f.write(text);<br />
        return item<br />
</code><br />
如果文件操作失败，可能是文件路径的问题，每个操作系统的路径不同，所以按你自己的系统改一下路径吧，直接调用os.path的方法就能获取当前绝对路径。</p>
<p>抓取到的文章都以title的名字命名，存放在tech目录下。会了这一个，我们还可以抓取其他不同的文章类型，比如财经类，军事类，历史类等等，只要改起始url，域名范围，rules正则表达式和回调函数中的页面解析。</p>
<p>最后在sina和tencent新闻里一共抓取了10000多篇文章，共10类，为以后我们的文本挖掘做基础。那这篇blog就到这里吧，竟然写了2个小时，我们下篇文本预处理见！</p>
