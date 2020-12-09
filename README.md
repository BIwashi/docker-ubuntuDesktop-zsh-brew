# dockerでUbuntu desktop環境構築（zsh + prezto + brew）

## 導入

```zsh
$ ./start.sh
```

## コンテナ

```zsh
$ docker-compose build --no-cache
$ docker-compose up -d

# コンテナに入る
$ docker-compose exec ubuntu_desktop zsh

# コンテナから出る
$ exit

# コンテナを削除
$ docker-compose down
```




---

## 参考
```zsh
# コンテナ内で実行
root@xxxxxxx:/# apt-get update
root@xxxxxxx:/# apt-get install ubuntu-desktop
root@xxxxxxx:/# exit #コンテナを再起動するためexit
```

```zsh
# ホスト側でコンテナを再起動
$ docker restart CONTAINER_ID
```
```zsh
# 再起動したコンテナ内からファイルマネージャーを起動
root@xxxxxxx:/# nautilus
```


## Linuxbrewを入れる
[DockerイメージにLinuxbrewをDockerfileでインストールする - Qiita](https://qiita.com/beeeyan/items/ef72532701bb8219bc55)



# ubuntu コンテナ内のguiをホストで表示するためのテスト

参考

[Docker上のGUI出力をMacで表示](https://qiita.com/uedashuhei/items/3f6f8612b5c4a2b00b1a)

[Dockerを導入してGUI操作可能なLinux(Ubuntu)コンテナを作成する
](https://qiita.com/ryoya-s/items/ee1daf9cab18c100c990)

<br>

![アーキテクチャ](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.amazonaws.com%2F0%2F100027%2F417679be-a834-d4f3-f887-b3939519eba4.png?ixlib=rb-1.2.2&auto=format&gif-q=60&q=75&w=1400&fit=max&s=0cc2c9ff5cf1454e141dcb6b6d9e3b46)

<br>

# Xquartzの導入・起動
Xquartz(X11.app)は、macOS用のX Window Systemとなります。X Window Systemとは、Unix系OSでGUI操作を可能にするものです。
最近のmacOSではデフォルトでは入っていないので、導入・起動します。

```
$ brew cask install xquartz
$ open -a XQuartz #起動
```
ちなみに、xquartzは下記から導入することもできます。

[Quartz](https://www.xquartz.org/)

```
# xquartzをセットアップ
brew cask install xquartz
# xquartzを起動しておきます
open -a XQuartz
```



## Socatの導入・起動
Socatは、簡単なプロキシサーバを建ててソケット通信を実施するものです。つまり、ここでは、port6000番(コンテナのGUI操作を行うx11用のボート)を通して、ホスト側の画面がコンテナ内と通信できるようにするために使用します。
まずは、導入から。

```
$ brew install socat
```

下記コマンドで、port6000番(UnixのGUI操作を行うxquartz用のポート)への通信をホスト側のディスプレイで表示するようにします。
forkは複数のコネクションを管理できるようにするオプションで、reuseaddrはポートを再利用可能にするオプションです。

```
$ socat TCP-LISTEN:6000,reuseaddr,fork UNIX-CLIENT:\"$DISPLAY\"
```


## ここから…
Qiitaの設定がいらない？


```
$ docker-compose build
$ docker-compose up -d
```


# dockerimagesのpush

[DockerImageをDockerHubに登録する方法 - Qiita](https://qiita.com/kon_yu/items/7c40f4dfbd1cce006ce7)

image名に大文字使うとバグる