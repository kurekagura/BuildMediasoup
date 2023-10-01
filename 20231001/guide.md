# mediasoup v3 セットアップ手順

## 環境

- Windows 10 Pro

MinGWをセットアップする際、BasicSetupからmsys-baseをインストールします。O:\sw\MinGW\msys\1.0\binにパスを通します。

```console
>where node & node -v
C:\sw\nodejs\node.exe
v16.13.1

>where python & python -V
O:\sw\Python311\python.exe
o:\sw\Anaconda3\python.exe
Python 3.11.5

>o:\sw\MinGW\bin\mingw-get.exe -V
mingw-get version 0.6.2-beta-20131004-1
Copyright (C) 2009-2013, MinGW.org Project
```

## mediasoupのインストール

```console
cd o:\sw
mkdir mediasoup & cd mediasoup
npm init
npm install mediasoup@3
```

mediasoup-client.jsを生成します。

```console
npm install browserify -D
npm install mediasoup-client@3 -D

mkdir public\js
node node_modules\browserify\bin\cmd.js node_modules\mediasoup-client\lib\index.js -s MediasoupClient -o public\js\mediasoup-client.js
```

## 動作確認

深謝：[yuhi69氏のリポジトリ](https://github.com/yuhi69/mediasoup-simple)のコードを利用させて頂きました（READMEを参照）。

必要なパッケージ

```console
npm install express
npm install @moebius/http-graceful-shutdown
npm install socket.io
```

最終的なpackage.json

```json
{
  "name": "mediasoup",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@moebius/http-graceful-shutdown": "^1.1.0",
    "express": "^4.18.2",
    "mediasoup": "^3.12.13",
    "socket.io": "^4.7.2"
  },
  "devDependencies": {
    "browserify": "^17.0.0",
    "mediasoup-client": "^3.6.101"
  }
}
```

## オレオレ・サーバ証明書の作成

以前、自前コンパイルしたOpenSSLを利用しました。

```console
>set PATH=O:\src\openssl\openssl\apps;%PATH%
>set OPENSSL_CONF=O:\src\openssl\openssl\apps\openssl.cnf

>openssl version
OpenSSL 1.1.1w  11 Sep 2023

mkdir certs & cd certs
openssl genrsa 2048 > server-key.pem
openssl req -new -key server-key.pem > server-csr.pem -subj "/CN=localhost"
openssl x509 -days 3650 -req -signkey server-key.pem < server-csr.pem> server-crt.pem
```

## Pythonについて

python-3.11.5-amd64.exeをインストールしました（但しAnacondaのみでもOKでした）。

```plaintext
☑Use admin privileges when installing py.exe
☑Add python.exe to PATH
→ Customize installation

□Documentation
☑pip
□tcl/tk and IDLE
□Python test suite
□py launcher ☑for all users(requires admin privileges)

□Install Python 3.11 for all users
□Associate files with Python(requires the 'py' launcher)
☑Create shortcuts for installed applications
☑Add Python to environment varialbes
□Precompile standard library
□Download debugging symbols
□Download debug binaries(requires VS 2017 or later)

Customize install location
O:\sw\Python311
```

【補足】当環境にはAnacondaがインストールされていたため、以下のPATH順序とした。

```plaintext
O:\sw\Python311\
O:\sw\Python311\Scripts\
----------------------------------
o:\sw\Anaconda3
o:\sw\Anaconda3\Scripts
o:\sw\Anaconda3\Library\bin
o:\sw\Anaconda3\Library\usr\bin
o:\sw\Anaconda3\Library\mingw-w64\bin
```

## 参考

- [mediasoup (WebRTC SFU)のインストール手順](https://moewe-net.com/webrtc/install-mediasoup)
- [mediasoupでWebRTC SFUを実装する](https://moewe-net.com/webrtc/how-to-mediasoup)
- [[Node.js] Windows10にオレオレ証明書でSSL通信のWebサーバを作る](https://qiita.com/suo-takefumi/items/3e3dcb1ae26b2efa2eb7)
- [Windowsでopensslを使う](https://kumasoft.blogspot.com/2016/09/windowsopenssl.html)
