## docSify

### 1.github创建仓库

![image-20191227103521942](.\images\image-20191227103521942.png)

### 2.本地选择路径并clone下来git

git clone https://github.com/260936767/javaDoc.git

### 3.全局安装docsify

npm i docsify-cli -g

```xml
D:\Documents\javaDoc>npm i docsify-cli -g
D:\nodejs\node_global\docsify -> D:\nodejs\node_global\node_modules\docsify-cli\bin\docsify

> docsify@4.10.2 postinstall D:\nodejs\node_global\node_modules\docsify-cli\node_modules\docsify
> opencollective-postinstall

Thank you for using docsify!
If you rely on this package, please consider supporting our open collective:
> https://opencollective.com/docsify/donate

npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.2.11 (node_modules\docsify-cli\node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.2.11: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

+ docsify-cli@4.4.0
added 314 packages from 169 contributors in 154.985s
```



### 4.初始化一个docsify项目

docsify init ./docs

```xml
D:\Documents\javaDoc>docsify init ./docs

Initialization succeeded! Please run docsify serve ./docs
```

### 5.本地预览

```xml
D:\Documents\javaDoc>docsify serve docs

Serving D:\Documents\javaDoc\docs now.
Listening at http://localhost:3000
```

![image-20191227104019235](.\images\image-20191227104019235.png)

### 6.发布到github上 GitHub Pages

1. git add .

2. git commit -m 'msg'

3. git push origin master


```xml
D:\Documents\javaDoc>git add .

D:\Documents\javaDoc>git commit -m  'msg'
[master (root-commit) 4ea4222] 'msg'
 3 files changed, 24 insertions(+)
 create mode 100644 docs/.nojekyll
 create mode 100644 docs/README.md
 create mode 100644 docs/index.html

D:\Documents\javaDoc>git push origin master
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (6/6), 709 bytes | 354.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To https://github.com/260936767/javaDoc.git
 * [new branch]      master -> master

```

### 7.github上的source保存选择

![image-20191227104336527](.\images\image-20191227104336527.png)

### 8.预览地址

https://260936767.github.io/javaDoc/

![image-20191227104418627](.\images\image-20191227104418627.png)