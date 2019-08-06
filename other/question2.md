# apidoc 教程

1. 首先，我们需要 node.js 的支持。在搭建好 node.js 环境后，通过终端输入 npm 命名进行安装。

    ```npm install apidoc -g```
    
    接着，我们还需要添加 apidoc.json 文件到项目工程的根目录下。
    
    ```
    {
         "name": "example",
         "version": "0.1.0",
         "description": "apiDoc basic example",
         "title": "Custom apiDoc browser title",
         "url" : "https://api.github.com/v1"
       }
    ```
    
    示例
    ```
    /**
     *   @api {GET} logistics/policys 查询签收预警策略
     *   @apiDescription 查询签收预警策略
     *   @apiGroup QUERY
     *   @apiName logistics/policys
     *   @apiParam  {Integer} edition   平台类型
     *   @apiParam  {String} tenantCode 商家名称
     *   @apiPermission LOGISTICS_POCILY
     */
    ```
2. 然后，在终端输入 apidoc 命令进行文档生成。用项目工程的根目录替代 myapp/,用需要生成文档的地址替代 apidoc/。

    ```
    apidoc -i myapp/ -o apidoc/ 
    ```

3. 运行
    ```
    docker run -it --name=apache -p 8088:80 -v /home/lhy/git/kycloudapi/apidoc:/var/www/html -d apache2:latest
    docker exec apache /etc/init.d/apache2 start
    ```

