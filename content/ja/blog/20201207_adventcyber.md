---
title: "Advent of Cyber 2020 Christmas"
date: 2020-12-10T22:00:00+09:00
draft: false

# post thumb
#image: "images/post/christmas.jpg"

# meta description
description: "教育的なCTF"

# taxonomies
categories:
  - "WriteUps"
tags:
  - ctf

# post type
type: "post"
contributors: [Yuri]
---

```本記事は旧ブログから移行しました```

## Advent Cyber 2020 やってみたら楽しかった。
{{< youtube BJF84oWHmok >}}
TryHackMe!という教育サイトのイベントで、教育的なCTFになっておりとても楽しいCTF。ご丁寧にサンタ帽の陽気な解説者によるYouTubeで解説動画もあり、辛いときはそれもヒントになるので、CTF解けなくて苦手意識感じている人にも向いている予感。
一時的な環境が作成され、VPNで接続してチャレンジする形式でウェブサイトの作りがなんだか凝っている。

### Day1 A Christmas Crisis
- VPNを繋ぐときにリージョンをAU-xxxにしないと繋がらない罠があるので注意。
- HTTP, Cookieについての解説記事が書かれている。
- フォーマット名はヒントの"shorthand for binary"で検索する。

```py
>>> 'もふもふ'.encode('utf-8').hex()
'e38282e381b5e38282e381b5'
>>> bytearray.fromhex("e38282e381b5e38282e381b5").decode()
'もふもふ'
```
- 変換サイト使ってしまったけれど、Pythonですぐ変換できる。
- Cookieeは書き換えてリロードすれば読み込まれる。


### Day2 The Elf Strikes Back!
- [php-reverse-shell](https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/master/php-reverse-shell.php)をphpとしてうまくアップロードさせて、リバースシェル状況を作ることでbashを奪うことが出来た。
  - Kali/AttackBoxに上のファイルがあるらしい。
- リバースシェルはAttackerがncコマンドのlisten mode（`sudo nc -lvnp 443`）を使うことで設定可能。
- 最初MacBookのncで-lが使えなかったが、`brew install netcat`でインストールしたコマンドであればOK
- [リバース シェル悪用の防御](http://help.sonicwall.com/help/sw/jpn/9620/26/2/4/content/Application_Control.079.23.html)
- [BashでTCP/UDP通信を行う | 俺的備忘録 〜なんかいろいろ〜](https://orebibou.com/ja/home/201605/20160529_001/)


### Day3  Christmas Chaos
- Burpの使い方講座。
- ブラウザのプロキシを使ってBurpに転送させ、リクエストを止めたりしつつ、使う。FoxyProxyも使う。
- ユーザー名とパスワードの組み合わせを自動で合わせてtryしてくれる機能もあった。
- [Check that Burp Suite's proxy listener is active - PortSwigger](https://portswigger.net/burp/documentation/desktop/getting-started/proxy-setup/check-listener)
- [Configuring Firefox to work with Burp - PortSwigger](https://portswigger.net/burp/documentation/desktop/getting-started/proxy-setup/browser/firefox)
- [Configuring your external browser to work with Burp - PortSwigger](https://portswigger.net/burp/documentation/desktop/getting-started/proxy-setup/browser)

### Day4  Santa's watching
- Fuzzing = fancy bruteforcing をやってみようという回
- GoBusterというツールで隠れファイルやディレクトリの検出ができる。
  - [OJ/gobuster: Directory/File, DNS and VHost busting tool written in Go](https://github.com/OJ/gobuster)
  - `gobuster dir -u http://example.com -w wordlist.txt -x php,txt,html`で単語帳に登録したワードをもとにファイルを探してくれる。
- wfuzzというツールでURL変数をwordlistに従ってリクエストを作成できる。
  - [xmendez/wfuzz: Web application fuzzer](https://github.com/xmendez/wfuzz)
  - `wfuzz -c -z file,wordlist -u "http://<URL>/api/xxxx.php?date=FUZZ"`
- wfuzzの実行時に"Pycurl is not compiled against Openssl"のエラーが出たが、Pythonでimport pycurlが出来なかったので、pycurlに問題が生じていたが、下記の情報で解決。
  - [Installing PycURL on macOS High Sierra - Chi Shang Cheng](https://cscheng.info/2018/01/26/installing-pycurl-on-macos-high-sierra.html)
  - `pip install --install-option="--with-openssl" --install-option="--openssl-dir=/usr/local/opt/openssl" pycurl`
- wordlist
  - [SecLists/Fuzzing at master · danielmiessler/SecLists](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing)
  - https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/big.txt

### Day5
- [SQLインジェクションチートシート](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/big.txt)
