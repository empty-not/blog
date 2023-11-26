---
title: "使用hugo搭建博客的详细教程"
date: 2023-11-25T14:25:40+08:00
draft: false
---
## 如何使用hugo搭建博客

- 注册github账号，新建一个仓库，名字随便定义

- 本地新建hugo站点

  ```
  hugo new site blog
  ```

- 对该hugo站点进行git初始化

  ```
  cd blog
  git init
  ```

- 加载自己想要的主题

  ```
  git submodule add https://github.com/yzccjg/dark-theme-editor.git themes/dark-theme-editor
  
  # https://github.com/yzccjg/dark-theme-editor.git是该主题的地址，
  # themes意思是加载到本地文件的这个目录下，
  # dark-theme-editor这个名字自定义
  
  加载完成后需要更改根目录下的hugo.toml文件，添加一行代码如下：
  theme = 'dark-theme-editor'
  ```

  

  - 创建一篇文章并测试是否正确

    ```
    hugo new 搭建hugo博客的详细教程.md
    将“搭建hugo博客的详细教程.md”中的
    draft: true改为draft: false
    随便写点内容，然后进行测试
    hugo server
    如果在浏览器中能够访问，说明一切正常
    ```

    

  - 将本地仓库和github远程仓库进行关联

    - 设置token，在github的开发者设置中，找到token设置，选择classic

    - 选择该token的权限，记得勾选workflow，如果忘记了勾选，后面也可以更改

    - 具体配置

      ```
      查看和清除旧的配置
      git config --global -l  #查看全局配置
      git config --global --edit  #删除全局配置，这个得会vim、
      
      下面两条是笨方法进行配置
      git config --global --unset user.name
      git config --global --unset user.email
      
      配置全局用户和邮箱，这个如果没有配置，在push代码时会自动提示
      git config --global  user.name "yzccjg"
      git config --global  user.email "yuzecheng520@qq.com"
      
      和远程仓库进行关联
      git remote remove origin  #删除远程仓库
      git remote add origin https://github.com/yzccjg/blog.git
      git remote set-url origin https://你的token@github.com/yzccjg/blog ##设置远程仓库origin的url
      git branch -M main  ##切换分支为main
      
      
      ```

  - 修改hugo.toml文件中的baseURL = 'http://example.org/' 使其与实际情况相对应

    ```
    baseURL = 'https://yzccjg.github.io/blog'
    ```

  - 配置workflow，在根目录下新建.github>workflows>gh-pages.yml,可以去hugo官网中拷贝相关配置，hugo官网为：https://gohugo.io/，>Docs>Hosting and deployment>Host on GitHub Pages

    ```
    # Sample workflow for building and deploying a Hugo site to GitHub Pages
    name: Deploy Hugo site to Pages
    
    on:
      # Runs on pushes targeting the default branch
      push:
        branches:
          - main
    
      # Allows you to run this workflow manually from the Actions tab
      workflow_dispatch:
    
    # Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
    permissions:
      contents: read
      pages: write
      id-token: write
    
    # Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
    # However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
    concurrency:
      group: "pages"
      cancel-in-progress: false
    
    # Default to bash
    defaults:
      run:
        shell: bash
    
    jobs:
      # Build job
      build:
        runs-on: ubuntu-latest
        env:
          HUGO_VERSION: 0.120.2
        steps:
          - name: Install Hugo CLI
            run: |
              wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
              && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
          - name: Install Dart Sass
            run: sudo snap install dart-sass
          - name: Checkout
            uses: actions/checkout@v4
            with:
              submodules: recursive
              fetch-depth: 0
          - name: Setup Pages
            id: pages
            uses: actions/configure-pages@v3
          - name: Install Node.js dependencies
            run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
          - name: Build with Hugo
            env:
              # For maximum backward compatibility with Hugo modules
              HUGO_ENVIRONMENT: production
              HUGO_ENV: production
            run: |
              hugo \
                --gc \
                --minify \
                --baseURL "${{ steps.pages.outputs.base_url }}/"          
          - name: Upload artifact
            uses: actions/upload-pages-artifact@v2
            with:
              path: ./public
    
      # Deployment job
      deploy:
        environment:
          name: github-pages
          url: ${{ steps.deployment.outputs.page_url }}
        runs-on: ubuntu-latest
        needs: build
        steps:
          - name: Deploy to GitHub Pages
            id: deployment
            uses: actions/deploy-pages@v2
    ```

    

  - 进行代码的推送，第一次推送可能会出错

    ```
    git add .
    git commit -m 'msg'
    git push -u origin main
    ```

  - 在github中配置该仓库的pages，具体为点击seting>pages>branch选择main

  - 更改本地文件，再次进行代码的推送，就会成功。

    ```
    git add .
    git commit -m 'msg'
    git push -u origin main
    ```

    

  ---

  下面是hugo的具体使用

  - 要使新建的文章直接就是可以发布的状态，而不是草稿的状态，在archetyoes文件夹下，修改default.md中的draft: false将原理的true改为false
  
  - 关于多级目录，应该在文件夹下新建一个_index.md文件，hugo才将这个文件夹识别为一个目录。该_index.md文件
  
    的配置一般为这样：
  
    ```
    说明：_index.md因为上面打不出来，上面将下划线_识别为了一种标记符
    title: "工学"
    weight: 1 #这个是权重，就是排序用的
    date: 2023-09-02T19:20:51+08:00
    draft: false
    ------下面的可以不写----------------
    author: Jing Wang
    tags: ["put", "some", "tags", "here"]
    categories: ["catgorized", "the", "page"]
    description: "Description of this page."
    keywords: "keywords,used,for,SEO"
    include_toc: true
    ```
  
    
  
    顶顶顶