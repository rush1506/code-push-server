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

CodePush Server is a CodePush progam server! microsoft CodePush cloud is slow in China, we can use this to build our's. I use [qiniu](http://www.qiniu.com/) to store the files, because it's simple and quick!  Or you can use local storage, just modify config.js file, it's simple configure.

## qq交流群 535491067

## 正确使用code-push热更新

- Apple allows the use of hot updates[Apple's developer agreement](https://developer.apple.com/programs/ios/information/iOS_Program_Information_4_3_15.pdf), But the provisions can not be prompted to update the user box, affecting the user experience. And Google Play happens to be the opposite, you must inform the user to update. However, China's android market must close the update box, otherwise it will be in the audit application to "please upload the latest version of the binary application package" to reject the application.
- react-native Different platforms bundle packets are not the same, in the use of code-push-server must be created when the different applications to distinguish (eg. CodePushDemo-ios and CodePushDemo-android)
- React-native-code-push only update the resource file, will not update java and Objective C, so npm upgrade depends on the package version, if dependent on the use of localized package, this time must change the application version number (ios modify Info.plist CFBundleShortVersionString, Android modifies versionName in build.gradle) and then recompiles the app to the app store.
- It is recommended to use the code-push release-react command to publish the application, which combines the package and release commands (eg code-push release-react CodePushDemo-ios ios -d Production)
- Each time you submit a new version to the App Store, you should also publish an initial version to the code-push-server based on the submission. (Since each time a code-push-server is released, the code-puse-server compares with the initial version to generate the patch)

## EXAMPLE 
Api.code-push.com is just a test server, do not put their own production environment on the project, the server broadband only 1M, and the service did not do load balancing and monitoring, stability can not guarantee, please yourself build their own services.

### shell命令行端

```shell
$ code-push login http://api.code-push.com:8080 #Login
```

### [web](http://www.code-push.com:8080) 

访问：http://www.code-push.com:8080

### Client eg.

[ReactNative CodePushDemo](https://github.com/lisong/code-push-demo-app)

[Cordova CodePushDemo](https://github.com/lisong/code-push-cordova-demo-app)

## INSTALL FROM NPM PACKAGE

```shell
$ npm install code-push-server -g
$ code-push-server-db init --dbhost localhost --dbuser root --dbpassword #Initialize the mysql database
$ code-push-server #Open the service in the browser http://127.0.0.1:3000
```

## INSTALL FROM SOURCE CODE

```shell
$ git clone https://github.com/lisong/code-push-server.git
$ cd code-push-server
$ npm install
$ ./bin/db init --dbhost localhost --dbuser root --dbpassword #Initialize the mysql database
$ ./bin/www #Open the service in the browser http://127.0.0.1:3000
```

## UPGRADE

*from source code*

```shell
$ cd /path/to/code-push-server
$ git pull --rebase origin master
$ ./bin/db upgrade --dbhost localhost --dbuser root --dbpassword #Upgrade the codepush database
$ #restart code-push-server
```

*from npm package*

```shell
$ code-push-server-db upgrade --dbhost localhost --dbuser root --dbpassword # Upgrade the codepush database
$ #restart code-push-server
```

## CONFIG
```shell
$ vim config/config.js
```
Please check if the following configuration is consistent with your environment, especially the downloadUrl parameter

```
  db: {
    username: "root",
    password: null,
    database: "codepush",
    host: "127.0.0.1",
    dialect: "mysql"
  },
  //The storage cloud configuration configuration needs to be configured when storageType is qiniu
  qiniu: {
    accessKey: "",
    secretKey: "",
    bucketName: "",
    downloadUrl: "" //File download domain name
  },
  //Ali cloud storage configuration needs to be configured when storageType is oss
  oss: {
    accessKeyId: "",
    secretAccessKey: "",
    endpoint: "",
    bucketName: "",
    prefix: "", // The prefix of the object Key is allowed to be placed inside the subfolder
    downloadUrl: "", // File download domain name address, need to include prefix
  },
  //The file is stored locally and needs to be configured when storageType is local
  local: {
    storageDir: "/Users/tablee/workspaces/storage",
    //File download address CodePush Server address + '/ download' download corresponding app.js inside the address
    downloadUrl: "http://localhost:3000/download",
    // public static download spacename.
    public: '/download'
  },
  jwt: {
    // Login jwt signature key, must be changed, otherwise there are security risks, you can use the randomly generated string
    // Recommended: 63 random alpha-numeric characters
    // Generate using: https://www.grc.com/passwords.htm
    tokenSecret: 'INSERT_RANDOM_TOKEN_KEY'
  },
  common: {
    dataDir: "/Users/tablee/workspaces/data",
    //Select the storage type, currently supports local, oss, qiniu, s3 configuration
    storageType: "local"
  },
```
read [config.js](https://github.com/lisong/code-push-server/blob/master/config/config.js)


## Storage mode [local/qiniu/s3]

- Configure local storage, modify config / config.js storageType value for the local, configuration in the following storageDir and downloadUrl local, if not on the same machine, downloadUrl Please specify the domain name or ip address


## RUN

```shell
$ node ./bin/www # or code-push-server
```

or point config file and ENV

```shell
$ CONFIG_FILE=/path/to/config.js NODE_ENV=production node ./bin/www # or CONFIG_FILE=/path/to/config.js NODE_ENV=production code-push-server
```

notice. you have to change `tokenSecret` in config.js for security.

## Default listen Host/Port  0.0.0.0/3000 
you can change like this.

```shell
$ PORT=3000 HOST=127.0.0.1 NODE_ENV=production node ./bin/www # or PORT=3000 HOST=127.0.0.1 NODE_ENV=production code-push-server
```

## [code-push-cli](https://github.com/Microsoft/code-push)
Use code-push-cli manager CodePushServer

```shell
$ npm install code-push-cli@latest -g
$ code-push login http://127.0.0.1:3000 #login in browser account:admin password:123456
```

change admin password eg.

```shell
$ curl -X PATCH -H "Authorization: Bearer mytoken" -H "Accept: application/json" -H "Content-Type:application/json" -d '{"oldPassword":"123456","newPassword":"654321"}' http://127.0.0.1:3000/users/password
```

## [react-native-code-push](https://github.com/Microsoft/react-native-code-push) for react-native

```shell
$ cd /path/to/project
$ npm install react-native-code-push@latest --save
```

## config react-native project
Follow the react-native-code-push docs, addition iOS add a new entry named CodePushServerURL, whose value is the key of ourself CodePushServer URL. Andriod use the new CodePush constructor in MainApplication point CodePushServerUrl

iOS eg. in file Info.plist

```xml
...
<key>CodePushDeploymentKey</key>
<string>YourCodePushKey</string>
<key>CodePushServerURL</key>
<string>YourCodePushServerUrl</string>
...
```

Android eg. in file MainApplication.java

```java
@Override
protected List<ReactPackage> getPackages() {
  return Arrays.<ReactPackage>asList(
      new MainReactPackage(),
      new CodePush(
         "YourKey",
         MainApplication.this,
         BuildConfig.DEBUG,
         "YourCodePushServerUrl" 
      )
  );
}
```


## [cordova-plugin-code-push](https://github.com/Microsoft/cordova-plugin-code-push) for cordova

```shell
$ cd /path/to/project
$ cordova plugin add cordova-plugin-code-push@latest --save
```

## config cordova project

edit config.xml. add code below.

```xml
<platform name="android">
    <preference name="CodePushDeploymentKey" value="nVHPr6asLSusnWoLBNCSktk9FWbiqLF160UDg" />
    <preference name="CodePushServerUrl" value="http://api.code-push.com:8080/" />
</platform>
<platform name="ios">
    <preference name="CodePushDeploymentKey" value="Iw5DMZSIrCOS7hbLsY5tHAHNITFQqLF160UDg" />
    <preference name="CodePushServerUrl" value="http://api.code-push.com:8080/" />
</platform>
```

## Production Manage
use [pm2](http://pm2.keymetrics.io/) to manage process.

```shell
$ npm install pm2 -g
$ cp config/config.js /path/to/production/config.js
$ vim /path/to/production/config.js #configure your env.
$ cp docs/process.json /path/to/production/process.json
$ vim /path/to/production/process.json #configure your env.
$ pm2 start /path/to/production/process.json
```

## Use [CodePush Web](https://github.com/lisong/code-push-web) manage apps

add codePushWebUrl config in ./config/config.js

eg.

```json
...
"common": {
  "codePushWebUrl": "Your CodePush Web address",
}
...
```

## License
MIT License [read](https://github.com/lisong/code-push-server/blob/master/LICENSE)


