# Selenium

在很多情况下，我们都需要一个能模拟用户在浏览器中行为的工具。可能我们会觉得抓包请求的方式过于复杂且容易被网站的反爬虫机制封杀，而我们很多情况下又不是非常在意性能，此时Selenium就是一个适合的选择。

本篇文章的主要内容是以Python语言为例，介绍Selenium及其基本的使用方式。

## Selenium简介

Selenium是一个将浏览器自动化的工具，一般所说的Selenium指的是Selenium WebDriver。Selenium还为各类语言提供了客户端使用户可以在各类语言里直接进行调用。

Selenium最初的目的是为网页提供自动化测试的工具，然而现在更多的被用在爬虫等领域。同时我也在我的文章自动发表工具中使用了Selenium，关于我的文章自动发表工具可查看[使用Python及Selenium自动发表文章 篇一：现状及技术选型](https://www.wangchucheng.com/zh/post/python-selenium-post-1/)。

## 环境搭建

由于本文是以Python为例的对Selenium的介绍，因此首先搭建好Python环境。Selenium对Python的环境要求为`Python 2.7`或`Python 3.4`以上。由于Python环境的搭建较为基础，本文中默认读者已经搭建好适合的Python环境。若要安装Selenium的Python客户端，只需输入`pip install selenium`即可完成安装。

由于Selenium需要对浏览器进行操作，因此除了Selenium，我们还需要下载浏览器驱动。需要说明的是，部分Selenium教程中使用的是PhantomJS，然而由于PhantomJS已于2018年后停止维护，因此不推荐使用。

我选用的是Chrome浏览器，因此需要下载Chrome对应的驱动。Selenium支持大多数主流浏览器，对应的驱动可以在如下地址中下载。

| 浏览器  | 驱动地址                                                     |
| ------- | ------------------------------------------------------------ |
| Chrome  | https://sites.google.com/a/chromium.org/chromedriver/downloads |
| Edge    | https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/ |
| Firefox | https://github.com/mozilla/geckodriver/releases              |
| Safari  | https://webkit.org/blog/6900/webdriver-support-in-safari-10/ |

浏览器的驱动与浏览器版本相关，因此需要下载对应版本的驱动。驱动下载后，需要将其加入环境变量中。

## Selenium快速体验

接下来我们将通过两个个非常简单的例子对Selenium进行初步的了解。

### 打开网页

```python
from selenium import webdriver

driver = webdriver.Chrome()
driver.get('https://wangchucheng.com/')
```

可以看到，只需进行简单的导入并初始化一个Chrome实例再输入网址即可进行访问。如果你在Python项目中使用的是`venv`，那么当你运行如上代码时，可能会遇到报错`selenium.common.exceptions.WebDriverException: Message: 'chromedriver' executable needs to be in PATH.`。对此最简单的方法是在初始化是指定浏览器驱动的路径即可，代码如下所示：

```python
from selenium import webdriver

driver = webdriver.Chrome('/path/to/driver')
driver.get('https://wangchucheng.com/')
```

代码成功运行后可以看到我的博客在浏览器中被打开，这样你就完成了Selenium的第一个示例。

ps：电脑需要安装Chrome浏览器，驱动文件放置于项目文件根目录内。

### 点击链接

在刚刚的例子中，我们已经能使用Selenium打开一个网页了。接下来我们将尝试点击网页中的链接，而这也是Selenium最常用的功能之一。

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
form selenium.webdriver.Chrome.by import By
driver = webdriver.Chrome()

driver.get('https://wangchucheng.com/')
assert '技术公馆' in driver.title

elem = driver.find_element(by=By.XPATH,value='//*[@id="featured"]/div/div/div[2]/div/h3/a')
elem.click()
```

> 此`xpath`路径在文章发表时正确，然而由于页面样式的不断变化，可能会存在失效的情况。如有失效欢迎大家在评论里为我指出。

通过以上的代码我们可以在访问界面后通过点击链接跳转到其他界面，在此我们使用的`xpath`来确定位置。`assert`则是用来判断打开的网站是否为自己所需要的，如不需要此步验证亦可不加。除`find_element_by_xpath`之外Selenium还支持通过`id`，`class_name`等方式进行获取，具体会在后文提到。

## Selenium页面交互

在刚刚的两个示例中，我们已经能打开一个网页并点击网页中的某个链接了。然而不论是将Selenium用于网页自动化测试亦或是爬虫，仅仅能打开网页是不够的，更需要的是与页面进行交互，接下来就是对此的介绍。

### 获取页面中的元素

Selenium为用户提供了多种方式来获取页面中的元素，用户可以选择最适合的方式进行使用，具体如下所示。需要说明的是，当使用`find_element_by`方法且有多个元素符合要求时，Selenium只会返回第一个符合要求的元素,没有找到则会抛出`NoSuchElementException`异常。

| 单一元素                            | 多个元素                             |
| ----------------------------------- | ------------------------------------ |
| `find_element_by_id`                | /                                    |
| `find_element_by_name`              | `find_elements_by_name`              |
| `find_element_by_xpath`             | `find_elements_by_xpath`             |
| `find_element_by_link_text`         | `find_elements_by_link_text`         |
| `find_element_by_partial_link_text` | `find_elements_by_partial_link_text` |
| `find_element_by_tag_name`          | `find_elements_by_tag_name`          |
| `find_element_by_class_name`        | `find_elements_by_class_name`        |
| `find_element_by_css_selector`      | `find_elements_by_css_selector`      |

刚刚我们的示例中使用的是`find_element_by_xpath`方法来获取元素，接下来的例子则是通过`find_element_by_link_text`来获取元素，其他获取方式和这两种方式相类似，读者可以自行进行尝试。

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()

driver.get('https://wangchucheng.com/')
assert '技术公馆' in driver.title

elem = driver.find_element_by_link_text('Hello, World!')
elem.click()
```

> 此`link_text`路径在文章发表时正确，然而由于页面样式的不断变化，可能会存在失效的情况。如有失效欢迎大家在评论里为我指出。

### 向文本框中输入文字

在我们使用Selenium时，最经常进行的操作之一就是向文本框中输入内容。Selenium输入内容时是通过模拟键盘敲击进行输入的，内容的输入可通过`send_keys`方法来完成。下图所示的为在我的博客中点击搜索按钮并输入搜索内容的代码。

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()

driver.get('https://wangchucheng.com/')
assert '技术公馆' in driver.title

search = driver.find_element_by_xpath('//*[@id="navbar-main"]/div/ul/li[1]/a')
search.click()

keyword =driver.find_element_by_id('search-query')
keyword.send_keys('Selenium')
```

> 此`xpath`及`id`路径在文章发表时正确，然而由于页面样式的不断变化，可能会存在失效的情况。如有失效欢迎大家在评论里为我指出。

按照以上方法即可做到点击搜索框并在搜索框中输入“Selenium”关键词了。

除了单一按键外，Selenium还支持同时输入多按键来达到更丰富的效果，其实现方式也非常的简单，例如输入`send_keys(Keys.CONTROL, 'c')`即代表复制。

在我们向文本框中输入内容时，有部分网站的占位符需要我们手动进行删除。如果直接在文本框中输入内容，由于占位符的存在可能无法获得期望的结果。这时我们就需要先将文本框内的内容清空。清空内容可通过`clear()`方法实现。

## Selenium页面切换

由于现代网站的不断变化，许多时候我们都会面临需要在多个窗口进行操作的情况。而Selenium也支持多窗口的操作。

### 窗口切换

在网页中点击链接后弹出新窗口是我们经常遇到的情况。如果后续操作需要在新网页上进行的话，切换窗口就是一个必要的功能。Selenium的窗口切换非常的简单，只需使用`switch_to_window('window_name')`来切换。

然而在很多情况下我们并不确定打开的网页的标题，因此可以使用窗口句柄`window_handles`来切换。窗口句柄中存储了浏览器中所打开的窗口，因此可以用来进行快速的切换。

以下代码所示的为在我的博客中点击Github按钮后切换到我的Github主页。

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()

driver.get('https://wangchucheng.com/')
assert '技术公馆' in driver.title

github = driver.find_element_by_xpath('//*[@id="profile"]/ul/li[2]/a')
github.click()

window_handles = driver.window_handles
driver.switch_to_window(window_handles[-1])
```

> 此`xpath`路径在文章发表时正确，然而由于页面样式的不断变化，可能会存在失效的情况。如有失效欢迎大家在评论里为我指出。

### 框架切换

除了不同窗口外，很多网站还存在这多个框架。这一点在相对较老的网站或有特定需求的网站中较为常见。例如，QQ的OAuth2登录界面就使用了`iframe`，而对于框架的切换Selenium也能进行处理。

和窗口切换类似，切换框架只需使用`switch_to_frame('frame_name')`即可。针对框架嵌套的情况，Selenium也对此进行了支持，使用`switch_to_frame('parent_frame.child_frame')`的形式，即可切换到子框架中。

### 对话框切换

在网页中，在许多情况中我们会收到来自对话框的提示，此使就需要对对话框中的内容进行处理。在处理之前，首先需要切换到对话框内。切换到对话框内的方式和之前的切换方式非常的相似，使用`switch_to_alert()`方法即可。

### 页面导航

除了弹窗以外，我们还经常会使用到网页的“前进”，“后退”和“刷新”等功能。这些功能在Selenium中也非常的简单。操作如下所示：

| 页面导航 | 多个元素    |
| -------- | ----------- |
| 前进     | `forward()` |
| 后退     | `back()`    |
| 刷新     | `refresh()` |

需要注意的是，这些方法的实现与浏览器驱动有关，因此你可能会遇到出乎意料的错误。以下代码为进入我的个人博客后点击一篇博文后进行后退，前进和刷新的操作。

```python
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

driver = webdriver.Chrome()

driver.get('https://wangchucheng.com/')
assert '技术公馆' in driver.title

elem = driver.find_element_by_link_text('Hello, World!')
elem.click()

driver.back()
driver.forward()
driver.refresh()
```

> 此`link_text`路径在文章发表时正确，然而由于页面样式的不断变化，可能会存在失效的情况。如有失效欢迎大家在评论里为我指出。

## Selenium页面等待

通过刚刚的示例我们已经能使用Selenium做到许多丰富的操作了。然而在尝试刚刚的示例时，你可能遇到过由于页面加载过慢，对应元素还未加载出来时，命令已经被执行而导致的`ElementNotVisibleException`异常。（如果没遇到过就证明我的博客访问速度尚可XD）。

尤其随着越来越多的网站都是通过接口从后端获得数据，此类问题就愈发明显。因此Selenium中提供了等待的相关方法，可以等待页面加载后再执行后续操作。

Selenium中的等待分为显式等待和隐式等待，接下来将对此进行基本介绍。

### 显式等待

显式等待是在访问界面后等待设定条件发生后再进一步的操作。需要注意的是，为了达到以上的目的读者应该避免使用`time.sleep()`方法，因为它设定了恒定的等待时间，会造成不必要地浪费。相应地，我们可以使用`WebDriverWait`来进行实现。

```python
from selenium import webdriver
from selenium.webdriver.support.wait import WebDriverWait

driver = webdriver.Chrome()

driver.get('https://wangchucheng.com/')
assert '技术公馆' in driver.title

try:
    elem = WebDriverWait(driver, timeout=5).until(lambda d: d.find_element_by_link_text('Hello, World!'))
    elem.click()
except Exception as e:
    print(e)
```

> 此`link_text`路径在文章发表时正确，然而由于页面样式的不断变化，可能会存在失效的情况。如有失效欢迎大家在评论里为我指出。

如上代码就是对刚刚示例代码的简单改动，我们将之前的`driver.find_element_by_link_text`改为了使用`WebDriverWait`的方式。通过设置超时时间可使网页在抛出TimeoutException异常之前默认以每500毫秒查询的方式直至结果成功返回或抛出`TimeoutException`异常。

`until`方法中的即为请求的预期条件。

### 隐式等待

如果某些元素不是立即可用的，隐式等待可以使WebDriver等待一定的时间后再进行查找。如不进行设置默认等待时间是0秒。

```python
from selenium import webdriver

driver = webdriver.Chrome()
driver.implicitly_wait(5)
driver.get('https://wangchucheng.com/')
```

隐式等待一般来说只应用在必须情况下，因为其会强制页面等待设定的固定时间。

## 浏览器定制

当你需要避免网站监测到浏览器是Selenium驱动，可以定制浏览器的属性达到最真实的用户浏览器。



## 后记

Selenium作为一个使用较为广泛的工具，使用难度也相对较低。以上功能虽然不是Selenium的全部功能，但也将Selenium的主要操作进行了涵盖。但除去如何使用这类术的问题，作为开发者更应思考的是应该在什么情况下使用Selenium才能发挥其最大价值。


sdf