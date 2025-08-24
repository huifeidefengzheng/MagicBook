# 1、生成秘钥库：

keytool将密钥（key）和证书（certificates）存储在keystore文件中，生成密钥库的同时生成一个密钥对，并且密钥库和密钥对都是需要密码的

输入的秘钥库口令为：changeit

```sh
keytool -genkey -keystore "E:/secret/test.keystore" -alias mytest -keyalg RSA -validity 365
```

![image-20230220203850757](E:\magic-book\javaee\java\https双休认证.assets\image-20230220203850757.png)

参数说明：
-genkey： 表示生成密钥对（公钥和私钥）

-keystore：每个 keytool 命令都有一个 -keystore 选项，用于指定 keytool 管理的密钥仓库的永久密钥仓库文件名称及其位置。如果不指定 -keystore 选项，则缺省密钥仓库将是宿主目录中（由系统属性的"user.home"决定）名为 .keystore 的文件。如果该文件并不存在，则它将被创建。

-alias：指定密钥条目的别名，该别名是公开的。

-keyalg：指定密钥的算法，如：RSA、DSA（如果不指定默认采用DSA）)

-validity：指定创建的证书有效期多少天

# 2、查看证书：

```sh
keytool -list -v -keystore test.keystore -storepass changeit
keytool -list -rfc -keystore ./test.keystore -storepass changeit
```

![image-20230220204144317](E:\magic-book\javaee\java\https双休认证.assets\image-20230220204144317.png)

参数说明：

-list 列出证书
-v 显示详细信息
-keystore 指定密钥库
-storepass 指定密钥库的解密密码
-rfc 以可编码方式打印证书

# 3、java生成HTTPS证书：

既然是双向验证，就需要双方的密钥，我们服务端称为localhost，而客户端称为client。需要生成双方的密钥文件，并把对方的cert导入自己的密钥文件里。整个过程如下：
注意：密码统一为：`changeit`,这个密码自己可以设置，然后记住就可以了。

## 3.1 生成服务端密钥文件localhost.jks：

```sh
keytool -genkey -alias localhost -keyalg RSA -keysize 2048 -sigalg SHA256withRSA -keystore localhost.jks -dname CN=localhost,OU=Test,O=pkslow,L=Guangzhou,C=CN -validity 731 -storepass changeit -keypass changeit
```

参数说明：
-genkey： 表示生成密钥对（公钥和私钥）

-keystore：每个 keytool 命令都有一个 -keystore 选项，用于指定 keytool 管理的密钥仓库的永久密钥仓库文件名称及其位置。如果不指定 -keystore 选项，则缺省密钥仓库将是宿主目录中（由系统属性的"user.home"决定）名为 .keystore 的文件。如果该文件并不存在，则它将被创建。

-alias：指定密钥条目的别名，该别名是公开的。

-dname 指定证书拥有者信息

-keyalg：指定密钥的算法，如：RSA、DSA（如果不指定默认采用DSA）)

-keysize 指定密钥长度

-validity：指定创建的证书有效期多少天

-storepass 指定密钥库的密码

-keypass 指定别名条目的密码

-list 显示密钥库中的证书信息

-v 显示密钥库中的证书详细信息

-export 将别名指定的证书导出到文件

-file 参数指定导出到文件的文件名

-delete 删除密钥库中某条目

-import 将已签名数字证书导入密钥库

-keypasswd 修改密钥库中指定条目口令

## 3.2 导出服务端的cert文件：

```sh
keytool -export -alias localhost -file localhost.cer -keystore localhost.jks
```

## 3.3 **生成客户端的密钥文件client.jks：**

```sh
keytool -genkey -alias client -keyalg RSA -keysize 2048 -sigalg SHA256withRSA -keystore client.jks -dname CN=client,OU=Test,O=pkslow,L=Guangzhou,C=CN -validity 731 -storepass changeit -keypass changeit
```

## 3.4 **导出客户端的cert文件：**

```sh
keytool -export -alias client -file client.cer -keystore client.jks
```

## 3.5 **把客户端的cert导入到服务端：**

```sh
keytool -import -alias client -file client.cer -keystore localhost.jks
```

## 3.6 **把服务端的cert导入到客户端：**

```sh
keytool -import -alias localhost -file localhost.cer -keystore client.jks
```

## 3.7 **检验服务端是否具有自己的private key和客户端的cert：**

```sh
keytool -list -keystore localhost.jks
```

为了建立连接，应该要把客户端的密钥文件给谷歌浏览器使用。因为JKS是Java的密钥文件格式，我们转换成通用的PKCS12格式如下：

**转换JKS格式为P12：**

```sh
keytool -importkeystore -srckeystore client.jks -destkeystore client.p12 -srcstoretype JKS -deststoretype PKCS12 -srcstorepass changeit -deststorepass changeit -srckeypass changeit -destkeypass changeit -srcalias client -destalias client -noprompt
```

# 4. 导入证书

```sh
keytool -import -alias test1 -file ./test.crt -keystore ./test.keystore -storepass changeit
```

# 5. 导出证书

```sh
keytool -export -alias mytest -keystore ./test.keystore -file ./test.crt -storepass changeit
```



# 6.修改密码

修改密钥库中指定条目的密码，格式：

```sh
keytool -keypasswd -alias 需修改的别名 -keypass 旧密码 -new 新密码 -storepass keystore密码 -keystore 所在的密钥库
```

修改密钥库的密码

```sh
keytool -storepasswd -keystore ./yushan.keystore(需修改口令的keystore) -storepass 123456(原始密码) -new yushan(新密码)
```

# 7.配置Spring boot

在application.properties或者application.yml中去配置即可：

```properties
server.port=443

server.ssl.enabled=true
server.ssl.key-store-type=JKS
server.ssl.key-store=classpath:localhost.jks
server.ssl.key-store-password=changeit
server.ssl.key-alias=localhost

server.ssl.trust-store=classpath:localhost.jks
server.ssl.trust-store-password=changeit
server.ssl.trust-store-provider=SUN
server.ssl.trust-store-type=JKS
server.ssl.client-auth=need
```

要将生成的localhost.jks文件放到springboot项目中的resource文件夹下，然后重启项目，就可以了
需要分别配置Key Store和Trust Store的文件、密码等信息，即使是同一个文件。
需要注意的是，server.ssl.client-auth有三个可配置的值：none、want和need。双向验证应该配置为need；none表示不验证客户端；want表示会验证，但不强制验证，即验证失败也可以成功建立连接。

# 8.**如何在浏览器中导入证书呢？**

打开谷歌浏览器，找到设置->隐私设置和安全性->管理设备证书->导入，按步骤提示导入就可以了，导入的是**client.p12**这个文件

![image-20230220224037868](E:\magic-book\javaee\java\https双休认证.assets\image-20230220224037868.png)

需要输入密码就是我们前面设置的changeit,当然你设置了其他的密码，正常输入就可以了。

![image-20230220224052773](E:\magic-book\javaee\java\https双休认证.assets\image-20230220224052773.png)

