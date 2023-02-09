---
layout: post
title: 크롬드라이버 자동 설치 chromedriver_autoinstaller
subtitle: chromedriver_autoinstaller
author: useok
banner:
  image: assets/images/banners/home.jpeg
  opacity: 0.618
  background: "#000"
  height: "40vh"
  min_height: "10vh"
  heading_style: "font-size: 2.5rem; font-weight: bold;"
  subheading_style: "color: gold"
categories: [python]
tags: [python]
sidebar: [article-menu,category-list] 
---
## chromedriver_autoinstaller

```python
import chromedriver_autoinstaller
from selenium import webdriver
import os

chrome_ver = chromedriver_autoinstaller.get_chrome_version().split('.')[0]
driver_path = "./{}/chromedriver".format(chrome_ver)

if os.path.exists(driver_path):
    print(f"chrom driver is insatlled: {driver_path}")
else:
    print(f"install the chrome driver(ver: {chrome_ver})")
    chromedriver_autoinstaller.install(True)

driver = webdriver.Chrome(driver_path)
```