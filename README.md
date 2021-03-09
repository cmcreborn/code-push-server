# Self Testing Code Push Server
原 [code-push-server](https://github.com/lisong/code-push-server) 做一些小测试跟更动, 欢迎讨论

## Basic 環境
### MacOS 11.2
- node：v14.15.5
- mySQL: 5.7.31 (注意最新版有加密修改问题,以及 sequelize支援的版本)
- OS: MacOS 11.2.1, 11.2.2
- code-push-cli: 2.1.6 (务必确认cli版本)
### Ubuntu 18.04
- node：v14.15.5
- mySQL: 5.7.33 (注意最新版有加密修改问题,以及 sequelize支援的版本)

## 注意事項
### node_modules相关
- 與原[code-push-server](https://github.com/lisong/code-push-server)上不同的地方(2021-02-26)：
  - 套件 lodash  (4.17.21)
  - 套件 log4js   6.3.0 
  - 套件 validator （10.11.0）
  - 套件 nodemailer 6.4.16 -> in case of Command Injection
  - 套件 pug 3.0.1 -> in case of Remote Code Execution
  - 套件 markdown-it 10.0.0 -> in case of Regular Expression Denial of Service
  - 移除 istanbul 
  - 套件 mocha  8.3.0 
  - 套件 sequelize 4.44.3 <NOTE> 修正4.x版 sql 注入issue, 后续需要升到6.x版 或另外更换套件
  - 套件 cos-nodejs-sdk-v5 2.9.9 
  - 套件 aliyun-sdk 1.12.3
  - 套件 upyun 3.4.4 
  - 套件 qiniu 7.3.2 -> in case of Regular Expression Denial of Service 
  - 套件 helmet 4.4.1 -> express 安全性提升
  - 套件 yargs 16.2.0
  - 套件 morgan 1.10.0
- Pm2 全局安裝 ==> pm2 install pm2-intercom 

### MySQL相关
- Mysql  執行前需要用可以修改root 帳密的工具先行改好臨時密碼

### config.js相关
- 記得設定好config.js內容：
  - db ==> mysql 帳密
  - Local ==> 根據設定檔案路徑 先行建立好相對資料夾
  - jwt ==> 務必修改加密token
- 资料夹 config 底下保留原本开源专案的config档案做备存,实际运行pm2 start process.json时依据该process.json里的 env 里的 CONFIG_FILE 定义来处理

### process.json相关
- json 注解烦请依照json正常格式, 另外key统一加上 "_//_" 前缀
- For PM2 running setting:
  - env.NODE_ENV <注意开发环境跟生产环境设定> 程式里有依据此参数做处理
  - env.CONFIG_FILE 吃config档案的路径设定
  - env底下参数有新增需再说明详列
  - 其馀参数参考pm2官方处理

## Mysql db 建立SCHEMA腳本使用範例

- 進入code-push-server 初始化mysql db, 创建codepush SCHEMA
  - local Mysql (建议产线不要用local)
```shell
$ ./bin/db init --dbhost localhost --dbport 3306  --dbuser root --dbpassword mypassword666
```
  - remote Mysql (记得setup Mysql remote access auth)
```shell
$ ./bin/db init --dbhost 192.168.20.15 --dbport 3306  --dbuser user02 --dbpassword mypassword666
```
## code修改記錄(與開源 2021-02-26相比)
- Buffer ==> 修正 new Buffer(), Buffer.from()問題 //[DEP0005]
- 增加 pm2 start process.json 配置 cluster時 log4js 修改參數
  ```javascript
  pm2: true,        
  disableClustering: true, 
  ```
- sequelize 4.44.3 api更换 findById -> findByPk, 后续需要再往上升级修正(因4.x版官方已不再维护, 5.x已计画停止, 需直上6.x)
- helmet 新版同源政策修改问题 暂定contentSecurityPolicy false, 后续需要优化改进


## 启动server
```shell
$ pm2 start process.json
```

# 以下保留原開源說明
# CodePush Server [source](https://github.com/lisong/code-push-server) 

[![NPM](https://nodei.co/npm/code-push-server.svg?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/code-push-server/)

[![NPM Version](https://img.shields.io/npm/v/code-push-server.svg)](https://npmjs.org/package/code-push-server)
[![Node.js Version](https://img.shields.io/node/v/code-push-server.svg)](https://nodejs.org/en/download/)
[![Linux Status](https://img.shields.io/travis/lisong/code-push-server/master.svg?label=linux)](https://travis-ci.org/lisong/code-push-server)
[![Windows Status](https://img.shields.io/appveyor/ci/lisong/code-push-server/master.svg?label=windows)](https://ci.appveyor.com/project/lisong/code-push-server)
[![Coverage Status](https://img.shields.io/coveralls/lisong/code-push-server/master.svg)](https://coveralls.io/github/lisong/code-push-server)
[![Dependency Status](https://img.shields.io/david/lisong/code-push-server.svg)](https://david-dm.org/lisong/code-push-server)
[![Known Vulnerabilities](https://snyk.io/test/npm/code-push-server/badge.svg)](https://snyk.io/test/npm/code-push-server)
[![Licenses](https://img.shields.io/npm/l/code-push-server.svg)](https://spdx.org/licenses/MIT)

CodePush Server is a CodePush progam server! microsoft CodePush cloud is slow in China, we can use this to build our's. I use [qiniu](http://www.qiniu.com/) to store the files, because it's simple and quick!  Or you can use [local/s3/oss/tencentcloud] storage, just modify config.js file, it's simple configure.


## Support Storage mode 

- local *storage bundle file in local machine*
- qiniu *storage bundle file in [qiniu](http://www.qiniu.com/)*
- s3 *storage bundle file in [aws](https://aws.amazon.com/)*
- oss *storage bundle file in [aliyun](https://www.aliyun.com/product/oss)*
- tencentcloud *storage bundle file in [tencentcloud](https://cloud.tencent.com/product/cos)*

## qq交流群 

- QQ群: 628921445
- QQ群: 535491067

## 正确使用code-push热更新

- 苹果App允许使用热更新[Apple's developer agreement](https://developer.apple.com/programs/ios/information/iOS_Program_Information_4_3_15.pdf), 为了不影响用户体验，规定必须使用静默更新。 Google Play不能使用静默更新，必须弹框告知用户App有更新。中国的android市场必须采用静默更新（如果弹框提示，App会被“请上传最新版本的二进制应用包”原因驳回）。
- react-native 不同平台bundle包不一样，在使用code-push-server的时候必须创建不同的应用来区分(eg. CodePushDemo-ios 和 CodePushDemo-android)
- react-native-code-push只更新资源文件,不会更新java和Objective C，所以npm升级依赖包版本的时候，如果依赖包使用的本地化实现, 这时候必须更改应用版本号(ios修改Info.plist中的CFBundleShortVersionString, android修改build.gradle中的versionName), 然后重新编译app发布到应用商店。
- 推荐使用code-push release-react 命令发布应用，该命令合并了打包和发布命令(eg. code-push release-react CodePushDemo-ios ios -d Production)
- 每次向App Store提交新的版本时，也应该基于该提交版本同时向code-push-server发布一个初始版本。(因为后面每次向code-push-server发布版本时，code-puse-server都会和初始版本比较，生成补丁版本)


### shell login

```shell
$ code-push login http://api.code-push.com #登录
```

### [web](http://www.code-push.com) 

访问：http://www.code-push.com

### client eg.

[ReactNative CodePushDemo](https://github.com/lisong/code-push-demo-app)

[Cordova CodePushDemo](https://github.com/lisong/code-push-cordova-demo-app)

## HOW TO INSTALL code-push-server

- [docker](https://github.com/lisong/code-push-server/blob/master/docker/README.md) (recommend)
- [manual operation](https://github.com/lisong/code-push-server/blob/master/docs/README.md)

## DEFAULT ACCOUNT AND PASSWORD

- account: `admin`
- password: `123456`

## HOW TO USE

- [normal](https://github.com/lisong/code-push-server/blob/master/docs/react-native-code-push.md)
- [react-native-code-push](https://github.com/Microsoft/react-native-code-push)
- [code-push](https://github.com/Microsoft/code-push)


## ISSUES

[code-push-server normal solution](https://github.com/lisong/code-push-server/issues/135)

[An unknown error occurred](https://github.com/lisong/code-push-server/issues?utf8=%E2%9C%93&q=unknown)

[modify password](https://github.com/lisong/code-push-server/issues/43)


# UPDATE TIME LINE

- targetBinaryVersion support
  - `*` 
  - `1.2.3`
  - `1.2`/`1.2.*`
  - `1.2.3 - 1.2.7`
  - `>=1.2.3 <1.2.7`
  - `~1.2.3`
  - `^1.2.3`


## Advance Feature

> use google diff-match-patch calculate text file diff patch

- support iOS and Android
- use `"react-native-code-push": "git+https://git@github.com/lisong/react-native-code-push.git"` instead `"react-native-code-push": "x.x.x"` in `package.json`
- change `apps`.`is_use_diff_text` to `1` in mysql codepush database

## License
MIT License [read](https://github.com/lisong/code-push-server/blob/master/LICENSE)


