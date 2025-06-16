### iptablesでファイアウォール設定

```iptables``` ```Firewall``` ```Ubuntu```   
<span style="color:gray">最終更新日 2025/06/16</span>

ファイアウォールと聞くと設定が難しいイメージがありますが、```iptables```を使用した以下の設定は簡単なのでぜひやってみましょう。   
簡単ですがセキュリティ対策にはなるはずです。   

## iptablesコマンド一覧 
【現状の設定を確認】　　　
```
$ sudo iptables -L -v -n
```

【許可するIPアドレスを追加】   
・新規で追加する場合   
```
$ sudo iptables -A INPUT -s <許可するIP> -d <宛先IP> -p tcp --dport 80 -j ACCEPT
```
・設定を任意の番号に挿入する場合
```
$ sudo iptables -I INPUT <挿入する位置> -s <許可するIP> -d <宛先IP> -p tcp --dport 80 -j ACCEPT
```

【他の接続を拒否】   
```
$ sudo iptables -A INPUT -p tcp --dport 80 -j DROP
```

【設定の永続化】   
```iptables-persistent```を使用します   
```
$ sudo apt-get install iptables-persistent
$ sudo netfilter-persistent save
```
これで再起動しても設定が消えません   

【番号を含めて設定一覧を表示】
```
$ sudo iptables -L --line-numbers
```

【番号を指定してルールを削除】
```
$ sudo iptables -D INPUT <番号>
```   

## 参考サイト
> https://qiita.com/ohtsuka-shota/items/7e168e7507d60ff6fd0f

