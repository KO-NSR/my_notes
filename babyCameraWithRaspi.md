### Raspberry pi zero 2 w でベビーカメラを作る
```RaspberryPiZero2W``` ```カメラ```   
<span style="color:gray">最終更新日 2025/06/13</span>


## 1. はじめに
2025年4月に息子が生まれ、育児休職中の父親です。   
育児中でも、手を放して他のことをしたい。という思いから、ベビーカメラを作ろうと思い立ちました。   
スマホからアクセスして別の部屋からも監視できる。という状態にしたいです。   

今回は、ラズパイを使用して自作しようと思います。
ラズパイを選んだ理由は、   
・たまたま家にあった（電子工作しようと思っていつしか購入していた）   
・サーバーに関する勉強になるかと思った   
・将来的には、温度、湿度センサなど追加してアレンジしていきたい   
こんなところです。   

調べてみると、**RPi-Cam-Web-Interface**というソフトウェアを使うと簡単に作れそうなので、これを使って進めてみます。   


## 2. 構成
【ハード】   
・[PC：RaspberryPiZero2W](https://www.amazon.co.jp/dp/B0DB2JBD9C?ref=ppx_yo2ov_dt_b_fed_asin_title)   
・[カメラ：OSOYOO IR-CUTカメラ](https://www.amazon.co.jp/dp/B0CD7KPH3K?ref=ppx_yo2ov_dt_b_fed_asin_title)   
・[カメラ用ケーブル：OSOYOO カメラケーブルセット](https://www.amazon.co.jp/dp/B0CCTB6V64?ref=ppx_yo2ov_dt_b_fed_asin_title)   
・電源   

【ソフト】   
・OS：Raspberry Pi OS (Legacy, 32-bit)    
・[ラズパイカメラ用ウェブインターフェース：RPi-Cam-Web-Interface](https://elinux.org/RPi-Cam-Web-Interface)   

## 3. RPi-Cam-Web-Interfaceのインストール
以下のサイトを参考に進めました
> https://elinux.org/RPi-Cam-Web-Interface (wiki)   
> https://www.mikan-tech.net/entry/rpi-cam-web-interface

必要な手順は5つだけ   
 1.ラズパイのカメラインターフェースの有効化   
 2.リポジトリの更新   
 3.gitのインストール   
 4.githubリポジトリのクローン   
 5.ディレクトリの移動   
 6.シェルスクリプトの実行   

以下がコマンドです   
```
$ sudo raspi-config   
$ sudo apt-get update   
$ sudo apt-get upgrade  
$ sudo apt install git   
$ git clone https://github.com/silvanmelchior/RPi_Cam_Web_Interface.git   
$ cd RPi_Cam_Web_Interface   
$ sudo ./install.sh   
```

これで完成です。以降はラズパイを起動するとこのソフトが自動で起動します。

少し躓いたところをメモします。   
ラズパイOSは、**Bullseye-32bit**を選びましょう。   
wikiにも書いてますが、64bitでは動作しません。   
また、現時点で最新OSのBookwormでも動作しませんので注意。 (gpacパッケージが利用できないことが理由みたい)   

## 4. 動作確認
インストール後は、ブラウザでラズパイのIPアドレスにアクセスすれば映像を確認できます。   
ローカルネットワーク内のデバイスから接続可能です。   
以下は接続時の画面です   
![test](./images/test.jpg)


IPアドレスを確認する場合は以下のコマンドをターミナルで実行してください
```
$ ifconfig
```

## 5. カメラ設置
今回は下の画像の様に、フレキシブルアームのスマホスタンド（100均です）を活用して取り付けてみました。   
![test](./images/test.jpg)   

実際の画像（夜中）※部屋は真っ暗です   
![test](./images/test.jpg)   

実際の画像（日中）      
![test](./images/test.jpg)   

今回購入したカメラは、昼夜の自動切り替え機能がついているので、とても便利です。   
赤外線LEDの光量は可変抵抗を回すだけで調整できますが、強くしすぎるとライト本体がかなり熱くなるので注意が必要です。   

夜中は、ラズパイの電源LED(緑)が少し気になったので、テープで隠しました...   

## 6. セキュリティ対策
このままではローカルネットワークに接続されている端末であれば、どの端末でもアクセスできてしまうので、ファイアウォールの設定を追加しようと思います。   
設定ツールは**iptables**を使用します。   

【現状の設定を確認】　　　
```
$ sudo iptables -L -v -n
```

【許可するIPアドレスを追加】   
```
$ sudo iptables -A INPUT -s 許可するIP -d 宛先IP -p tcp --dport 80 -j ACCEPT
```

【他の接続を拒否】   
```
$ sudo iptables -A INPUT -p tcp --dport 80 -j DROP
```

【設定の永続化】   
```iptables-persistent```を使用します   
```
$ sudo apt-get install iptables-persistent
$ netfilter-persistent save
```
これで再起動しても設定が消えません   

【番号を含めて設定一覧を表示】
```
$ sudo iptables -L --line-numbers
```

【番号を指定してルールを削除】
```
$ sudo iptables -D INPUT 番号
```


参考サイト
> https://qiita.com/ohtsuka-shota/items/7e168e7507d60ff6fd0f



## 7. 今後の運用
冒頭に書いた、サーバーの基礎勉強に関してですが、今回のソフトを使うと自分で考える部分がほとんど無いので、正直あまり勉強になっていません。   
自分で一から作ったら。という想定で少し勉強してみます。   

ひとまずこの状態で何日か運用してみます。ラズパイゼロ2Wの処理能力も気になるので...   
動態検知やタイムラプス撮影もできるみたいなので、寝返りを打った時の映像や寝相なんかも撮影してみると面白そうです。   
また、動態検知したらメールでお知らせすることもできたり、温度センサや湿度センサも追加して表示させたりしたいので、もう少し深く触ってみます。   
今回はここまで...   

