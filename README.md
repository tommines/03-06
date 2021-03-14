### 解答题

1. #### 说说 application/json 和 application/x-www-form-urlencoded 二者之间的区别。

   在浏览器原生form 表单，如果不设置enctype属性，那么默认就以 application/x-www-form-urlencoded 方式提交数据。

   Content-Type 被指定为 application/x-www-form-urlencoded，提交的数据按照 key1=val1&key2=val2 的方式进行编码，key 和 val 都进行了 URL 转码。支持比较好，比如jquery 就是默认使用 application/x-www-form-urlencoded;charset=utf-8。

   application/json是现在流行的使用JSON规范，浏览器的支持度也越来越好，可以方便的提交复杂的结构化数据，特别适合 RESTful 的接口。

   

2. #### 说一说在前端这块，角色管理你是如何设计的。

   角色管理涉及用户管理与权限管理

   在角色管理里面添加，修改，删除角色，然后对这个角色赋予可以使用那些菜单的权限，及菜单里的操作的权限，然后在用户管理里添加这个角色，使用户拥有这个角色的权限。

3. #### @vue/cli 跟 vue-cli 相比，@vue/cli 的优势在哪？

   性能提升，diff方法的优化，vue3新增了静态的标记（pathflag),按需编译,ssr渲染，体积更小,更好支持ts

   

4. #### 详细讲一讲生产环境下前端项目的自动化部署的流程。

   环境准备

   Linux服务器

   把代码提交到GitHub远程仓库


   先在GitHub上建一个仓库，realworld-nuxtjs，将本地代码提交到仓库里。


   配置GitHub Access Token


   生成：https://github.com/settings/tokens


   头像 -> Settings -> Developer settings -> Personal access tokens -> Generate new Token


   Token名称填写Tocken，Select scopes勾选repo，然后滚动到网页最下面点击提交按钮。生成了Token

   配置到项目的Secrets中：https://github.com/xxx/realworld-nuxtjs/settings/secrets/new

   回到项目https://github.com/xxx/realworld-nuxtjs下面


   Settings -> Secrets -> New Secrets


   配置GitHub Actions执行脚本


   在项目根目录创建.github/workflows目录


   下载main.yml到workflows目录中：https://github.com/xxx/realworld-nuxtjs/edit/master/.github/workflows/main.yml


   修改配置 main.yml

   下载下来main.yml是用npm来管理项目的

   修改对应的服务器路径为：/realworld-nuxtjs

   wget后面的下载地址改为自己的仓库地址：https://github.com/xxx/realworld-nuxtjs/releases/latest/download/release.tgz


   在项目里https://github.com/xxx/realworld-nuxtjs/settings/secrets/new配置HOST、USERNAME、PASSWORD、PORT

   最终我的main.yml

   name: Publish And Deploy Demoon:

    push:
    
     tags:
    
      \- 'v*'

   jobs:

    build-and-deploy:
    
     runs-on: ubuntu-latest
    
     steps:


     **# 下载源码**
    
     \- name: Checkout
    
      uses: actions/checkout@master


     **# 打包构建**
    
     \- name: Build
    
      uses: actions/setup-node@master
    
     \- run: yarn
    
     \- run: yarn build
    
     \- run: tar -zcvf release.tgz .nuxt static nuxt.config.js package.json yarn.lock pm2.config.json


     **# 发布 Release**
    
     \- name: Create Release
    
      id: create_release
    
      uses: actions/create-release@master
    
      env:

   ​    GITHUB_TOKEN: ${{ secrets.TOKEN }}

      with:

   ​    tag_name: ${{ github.ref }}

   ​    release_name: Release ${{ github.ref }}

   ​    draft: false

   ​    prerelease: false

​    

     **# 上传构建结果到 Release**
    
     \- name: Upload Release Asset
    
      id: upload-release-asset
    
      uses: actions/upload-release-asset@master
    
      env:

   ​    GITHUB_TOKEN: ${{ secrets.TOKEN }}

      with:

   ​    upload_url: ${{ steps.create_release.outputs.upload_url }}

   ​    asset_path: ./release.tgz

   ​    asset_name: release.tgz

   ​    asset_content_type: application/x-tgz

​    

     **# 部署到服务器**
    
     \- name: Deploy
    
      uses: appleboy/ssh-action@master
    
      with:

   ​    host: ${{ secrets.HOST }}

   ​    username: ${{ secrets.USERNAME }}

   ​    password: ${{ secrets.PASSWORD }}

   ​    port: ${{ secrets.PORT }}

   ​    script: |

   ​     cd /product/front/realworld-nuxtjs

   ​     wget https://github.com/xxxxx/realworld-nuxtjs/releases/latest/download/release.tgz -O release.tgz

   ​     tar zxvf release.tgz

   ​     npm install --production

   ​     pm2 reload pm2.config.json


   配置PM2配置文件 pm2.config.json ,pm2使用npm来起项目。


   {

    "apps": [
    
     {
    
      "name": "RealWorld",
    
      "script": "npm",
    
      "args": "start"
    
     }
    
    ]}

   


   提交更新

   · git add .

   · git commit -m"第一次发布部署-测试"

   · git push （此时只是推送了提交记录，并不会触发自动化构建）

   · git add .

   · git tag v0.1.0 （通过tag打版）

   · git tag （查看版本）

   · git push origin v0.1.0 （把本地标签推送到远程仓库，会触发自动构建）

   

   项目主页的右侧还可以点进去看到发布日志以及版本和源代码


   访问网站：xxx.com

   http://xxx.xxx.com/

   nginx配置如下：

   upstream realworld {

   ​    server 127.0.0.1 max_fails=7 fail_timeout=7s;}

   server {

   ​    listen 80;

   ​    server_name strapi.jiailing.com;

   ​    location / {

   ​        proxy_pass http://xxx;

   ​    }}

5. #### 你在开发过程中，遇到过哪些问题，又是怎样解决的？请讲出两点。
    
    1.有次在开发后台的时候，之前开发好的，有数据，第二天再次打开，发现提示403，经过对代码的检查，发现没有问题。然后再打开视频提供的后台用同一个账户登录发现没有权限展示出来了，确定可能被其他人给操作，导致无权限。
2.在开发过程中提示错误，可以通过浏览器的console 等查看错误提示，经行排查，发现是导入的名称写错了，修改好，恢复正常。
    
6. #### 针对新技术，你是如何过渡到项目中？
    
    技术日新月异，不断更新，首先得了解新技术的对比，其中的优势，是否稳定，然后逐渐早老项目中更替