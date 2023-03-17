---
title: Blog Data Migtation
date: 2023-03-16 02:47:00 -0500
categories: [Walkthrough, Python]
tags: [python, moving files, data, strapi, frontmatter, glob]
published: true
---

Okay so I have a problem. I've done about ~200 posts on this site but I also have this template that I am using called chirpy. We'll I eventually want to design my own in the future, but I need all of these posts to display as well. 

Strapi is an open source Node.js headless CMS. I have installed such an instance on an ubuntu server inside of my network. So my gameplan is so use python to read all of the markdown files I have on my computer and transfer them into the strapi instance I have installed. That way I can keep updating my blog and projects, but actually be able to edit my posts and such inside of my own web application that I have designed. Then I can use the API to retreive/update/create new posts.

## Creating the Program

So I should start out by creating a script that can read the information I need from all of my markdown files.

### Extracting Information 

So the format of my .md files are they start with headers as such.

```md
---
title: Blog Data Migtation
date: 2023-03-16 02:47:00 -0500
categories: [Walkthrough, Python]
tags: [python, moving files, data, strapi]
published: true
---
```

 I can do away with the date information because that will be updated when I upload the information, but I would like to keep the title, categories, tags, and content.

 My blog files are formatted with jekyll YAML so we can use [frontmatter](https://pypi.org/project/python-frontmatter/) which will do the heavy lifting for us.

 We will use glob to iterate over all of the files in the directory, then we can create a function that will use the frontmatter module to extract the information we need from the markdown files.

```python
import glob
from frontmatter import Frontmatter
from poster import PostInfo


folder_path = "E:\Documents\GitHub\\0xskar.github.io\_posts\\"


# Extract information from the file
def extract_info(file_path):
    post = Frontmatter.read_file(file_path)
    title = post['attributes']['title']
    # If missing a few of the fields in the page it will replace with None
    try:
        categories = post['attributes']['categories']
    except KeyError:
        categories = post.get('categories')
    try:
        tags = post['attributes']['tags']
    except KeyError:
        tags = post.get('tags')
    content = post.get('body')
    return title, categories, tags, content


# Read all the markdown files in directory
for file_path in glob.glob(folder_path + '/*.md'):
    title, categories, tags, content = extract_info(file_path)
	PostInfo(title, categories, tags, content)
```
{: file="main.py" }


Everything seems to be working properly, so meow with the extracted information can create the PostInfo class that will handle posting of the information to the strapi api.

```python
import os
import requests


class PostInfo:
    """ Get post information, connect to strapi, post information """
    def __init__(self, title, categories, tags, content):
        self.api_key = os.environ['STRAPI_API_KEY']
        self.endpoint = "http://192.168.0.35:1337/api/articles"
        self.headers = {
            "Authorization": f"Bearer {self.api_key}"
        }
        self.title = title
        self.categories = categories
        self.tags = tags
        self.content = content
        self.post()

    def post(self):
        schema = {
            "data": {
                "title": self.title,
                "content": self.content,
                "categories": self.categories,
                "tags": self.tags
            }
        }
        r = requests.post(url=self.endpoint, headers=self.headers, json=schema)
        print(r.status_code)

```
{: file="poster.py" }

Everything works. Now can carry on working on the design to display my articles.
