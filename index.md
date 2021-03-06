# DNS基本のキ

## route 53

![_2015-09-09_0_50_01](https://cloud.githubusercontent.com/assets/1235432/9739950/2cddc646-568d-11e5-8920-ceb2b29de007.png)

# DNS、理解できてるか？


### 参考1
Software Design 2015 4月号


<img src="http://ecx.images-amazon.com/images/I/71RV3pB-F0L.jpg" width="400px">

### 参考2

``` 
株式会社日本レジストリサービス（JPRS）
DNSチュートリアル

https://www.janog.gr.jp/meeting/janog35/index.php/download_file/view/83/175/
```


# ポイント

```
DNSには常に2つの意味がある!
```

## DNSの種類

___DNS___には以下の2つの意味がある
    
1. DNSの階層構造を``` 構成する ```(分散管理)
2. DNSの階層構造を``` たどる```(名前解決)

## DNSサーバの種類

___DNSサーバ___には以下の2つの意味がある

1. ``` 権威DNSサーバ ```
	* 階層構造を ___構成___ する
	* インターネット全体を対象
	* Route 53
2. ```キャッシュDNSサーバ```
	* 階層構造を ___たどる___
	* 通常はISP内や組織内に限定
	* google public DNS


```
「DNSサーバ」には2つの意味があるので、
使い分ける所から第一歩
```


## DNSサーバ構成
![2015-09-09 1 13 33](https://cloud.githubusercontent.com/assets/1235432/9740506/0c31ea96-5690-11e5-99ac-3d0742694d81.png)

## 名前がややこしい

* キャッシュDNSサーバ
  * フルリゾルバー、フルサービスリゾルバー
* 権威DNSサーバ
  * DNSコンテンツサーバ、権威ネームサーバ、ゾーンサーバ

## 2種類のクエリ
1. ___再帰的クエリ___
	* クライアント -> キャッシュDNSサーバ
	* 名前解決お願い!!(依頼)
	* RD=1
2. ___非再帰的クエリ___
	* キャッシュDNSサーバ -> 権威DNSサーバ
	* 実際に名前引くよ!!(実行)
	* キャッシュの有無によってはクエリは実行されない
	* RD=0

```
ここを区別できてないと問題の切り分けができない
```

## キャッシュという諸刃の剣
* メリット
	* 名前解決の負荷、時間短縮	
* デメリット
	* ドメイン名の迅速な対応づけができなくなる

```
キャッシュ可能な時間(TTL)で調整
```

## ネガティブキャッシュ

* 顧客から「登録したドメイン名が利用できない」と連絡がきたとき
* 顧客が利用するキャッシュDNSサーバー以外は名前が引けるとき

```
ネガティブキャッシュが原因かも？
```

* 「そのドメイン名は存在しない」「その情報は存在しない」という情報のキャッシュ
* ドメイン名の登録設定（親ゾーンからの委任の設定）が行われる前に名前を引こうとすると、ネガティブキャッシュが残ってしまう

## digコマンド
```sh
dig option @dnsserver domain qurytype
```

```sh
dig +norec @ns1.jprs.jp jprs.jp A
```


* オプション
	* `+recurse` or `+rec`
		* 再帰的クエリ
		* 階層構造をたどって
	* `+norecurse` or `+norec`
		* 非再帰的クエリ
		* 持ってる情報教えて

## まとめ
* DNSには2種類の意味がある事を常に意識
* DNSサーバ
* DNSクエリ
* digを使ってみよう