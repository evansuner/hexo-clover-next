---
title: 使用selenium + lxml自动爬取engoo daily-reading articles并写入文件
date: 2022-02-23 16:55:10.549
updated: 2022-02-23 21:31:19.622
url: /?p=221
categories: 
- 爬虫
tags: 
- Python
- selenium
- lxml
---


engoo是一个学习英语的网站，通过阅读每日新闻，回答问题可以提高你的英语语感，与人交流时对不同的话题有更多的见解 我每天都会阅读两篇文章，然后制作成markdown记录下来，但是日复一日的拷贝文章，复制问题真的非常的麻烦。所以这里我决定使用脚本将它们爬取下来，这样我只要每天运行一次将获取到的文件拷贝到markdown里即可。   
刚开始我准备使用`requests + lxml`直接爬取文章的，但是我发现该网站使用 `google tags manager`，使用requests的话完全拿不到数据。最终使用`selenium + lxml`

## requirements.txt
```python
lxml==4.8.0
selenium==4.1.0
```
## 导库
```python
import datetime
import time
from selenium import webdriver
from lxml import etree
# from selenium.webdriver.chrome.options import Options
```
## 代码
```python
ch_option = Options()
# 该网站使用无头浏览器会拿不到数据
# ch_option.add_argument("--headless")

driver = webdriver.Chrome(options=ch_option)
driver.get("https://engoo.com/app/daily-news")

time.sleep(5)
page_source = driver.page_source
tree = etree.HTML(page_source)
false_urls = tree.xpath('//*[@id="content"]/div[1]/div[1]/div[2]/div/div[2]/a/@href')
article = """"""
for index, url in enumerate(false_urls):
    true_url = "https://engoo.com" + url
    # print(true_url)
    driver.get(true_url)
    time.sleep(5)
    # print(driver.page_source)
    tree = etree.HTML(driver.page_source)
    title = tree.xpath('//*[@id="windowexercise-2"]/div/div/div/div[3]/div/div[1]/div/div/span/span/span/text()')[0]
    contents_list = tree.xpath('//*[@id="windowexercise-2"]/div/div/div/div[3]/div/p/span/span/span/text()')
    contents = """"""
    for p in contents_list:
        contents = contents + "\n" + f"<p>{p}</p>"

    # print(contents)
    session_name = tree.xpath(
        '//*[@id="windowexercise-3"]/div/div/div/div[1]/div[2]/div/span[1]/span/span/span/text()'
    )[0]
    # print(session_name)
    questions = """"""
    discussion = """"""
    further_discussion = """"""
    if session_name == "Questions":
        questions_list = tree.xpath(
            '//*[@id="windowexercise-3"]/div/div/div/div[3]/div/div/div/div[1]/div/div[2]/div/div/div/span/span/span/text()'
        )
        for q in questions_list:
            questions = questions + "\n" + f"+ {q}   \n<font style='color:green'></font>\n"

        discussion_list = tree.xpath(
            '//*[@id="windowexercise-4"]/div/div/div/div[3]/div/div/div/div[1]/div/div[2]/div/div/div/span/span/span/text()'
        )
        for d in discussion_list:
            discussion = discussion + "\n" + f"+ {d}   \n<font style='color:green'></font>\n"

        further_discussion_list = tree.xpath(
            '//*[@id="windowexercise-5"]/div/div/div/div[3]/div/div/div/div[1]/div/div[2]/div/div/div/span/span/span/text()'
        )
        for f in further_discussion_list:
            further_discussion = further_discussion + "\n" + f"+ {f}   \n<font style='color:green'></font>\n"
    else:
        discussion = """"""
        discussion_list = tree.xpath(
            '//*[@id="windowexercise-3"]/div/div/div/div[3]/div/div/div/div[1]/div/div[2]/div/div/div/span/span/span/text()'
        )
        for d in discussion_list:
            discussion = discussion + "\n" + f"+ {d}   \n<font style='color:green'></font>\n"
        further_discussion = """"""
        further_discussion_list = tree.xpath(
            '//*[@id="windowexercise-4"]/div/div/div/div[3]/div/div/div/div[1]/div/div[2]/div/div/div/span/span/span/text()'
        )
        for f in further_discussion_list:
            further_discussion = further_discussion + "\n" + f"+ {f}   \n<font style='color:green'></font>\n"

    article1 = f"""## Article{index + 1}\n\n**[{title}]({true_url})**\n{contents}\n\n**Question**\n{questions}\n\n**Discussion**\n {discussion}\n\n**Further discussion**\n{further_discussion}
        """
    article = article + article1 + "\n"

# print(article)
with open('daily-read.txt', 'w', encoding='utf-8') as fp:
    print("正在写入...")
    fp.write(f'# {datetime.datetime.now().strftime("%d/%m/%Y")}\n' + article)
driver.quit()

```

最后得到的txt文件符合mardown格式，每天我只需要将txt中的文本复制到typora中即可。简单快捷