## yarn对比npm
1. 安装
    - npm,建议设置npm镜像以加速后面的过程（注意:不要使用cnpm，cnpm安装路径比较奇怪）
    ```
    npm config set registry https://registry.npm.taobao.org --gobal
    npm config set disturl https://npm.taobao.org/dist --global
    ```
    - yarn是facebook替代npm的工具
    ```
    npm install -g yarn
    yarn config set registry https://registry.npm.taobao.org --global
    yarn config set disturl https://npm.taobao.org/dist --global
    ```

2. npm命令对应的yarn命令
    ```
    npm init ---- yarn init
    npm run  ---- yarn run
    npm install ** ---- yarn install **
    npm uninstall ** --save ---- yarn remove **
    npm install ** --save-dev ---- yarn add ** --dev
    npm update ** --save ---- yarn upgrade **
    npm install ** --global ---- yran global add **
    ```
3. 离线安装
    - 本地安装过一个包，那么会被缓存，下次在其他地方安装的时候，可以使用离线模式
    ```
    yarn add ** -offline
    ```
4. 版本依赖一致性
    - yarn add生成的yarn.lock会指定各个依赖的确定版本，使得在不同机子上安装依赖时版本保持一致