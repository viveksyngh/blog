---
title: "Download a file from URL in Python"
date: 2018-02-03T01:05:59+05:30
categories: ["urllib", "python2", "python3"]
author: "Vivek Singh"
---

This blog post is about how to download an image or other static content from a URL in python.

I am going to accomplish this without any additional python package. We will look at two different solutions for Python2 and Python3. So Let’s get started.

Before we start downloading the file, we need to get the filename from the URL. The string after the last backslash in URL is the filename.

```python
def get_filename(url):
    if url.find('/'):
        return url.rsplit('/', 1)[1]
```
Above snippet of code will return the filename.

#### Python2:

```python
import urllib

def download_file(url):
    filename = get_filename(url)
    if filename is None:
        print("Filename not found")
        return
    urllib.urlretrieve(url, filename)
```

#### Python3:
```python
import urllib.request

def download_file(url):
    filename = get_filename(url)
    if filename is None:
        print("Filename not found")
        return
    urllib.request.urlretrieve(url, filename)
```
