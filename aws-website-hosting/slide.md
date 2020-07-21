---
marp: true
paginate: true
html: true
style: |
  header {
    font-size: 32px;
    color: #999;
    font-weight: 600;
  }
  img {
    background-color: #333;
  }
  img[alt~="center"] {
    display: block;
    margin: 0 auto;
  }
  img[alt~="right"] {
    display: flex;
    float: right;
  }
  section::after {
    background-color: #A30000;
    width: 100%;
    font-size: 10px;
    content: tomtsutom;
  }
  section {
    color: #ccc;
  }
  section.title {
    justify-content: center;
    text-align: center;
  }
  section.title p{
  }
  section.wide-margin-list li{
    margin-bottom: 1.5em;
  }
  section.contents {
    justify-content: start;
    float: left;
    padding: 78.5px 40px;
  }
  section.contents::before {
    content: '';
    display:block;
    padding-top:10px;
  }
  a {
    color: #c73a3a;
  }
  h1 {
    color: #fff;
  }
  h2 {
    color: #fff;
  }
  h1, h2, h3, h4, h5 {
    margin: 0.7em 0 0.5em 0;
  }
  ul {
    padding: 0 1em;
    margin: 0 40px;
  }
  p {
    padding: 0 1em;
    margin: 0 40px;
  }
  .image p {
    padding: 0;
    margin: 0;
  }

---
<!--
class: title
backgroundColor: #333;
_paginate: false
-->

# AWS放浪記
### 格安ホスティングを目指して

21卒エンジニア職 池田 力

---
<!--
_header: 背景
class: contents
-->

# 格安で、独自ドメインなサイトをちゃちゃっと作りたい！！

## 自分で自由に改造できるサイトを作りたかった
note, Qiita, はてなブログだとできてスタイル改造程度

## AWSを活用すると安い(し、なんかかっこいい)
サーバーレスでサイトを構築すると格安でサイトを作ることができる

## 独自ドメインのサイトにしたい

## ( GitHub Actionsの有効活用 )

---
<!--
class: title
-->
# どうやったか / 実際にやってみた結果
<br>
AWS編<br>
<br>
フロントエンド編<br>

---
<!--
_header: どうやったか - AWS編
class: contents
-->

## Google検索するとわんさか情報が出てくる
![center height:500px](./Google検索結果.jpg)

---
<!--
_header: どうやったか - AWS編
class: contents
-->

## 最終的な構成
![center height:500px](./architecture.jpg)

---
<!--
_header: どうやったか - AWS編
class: contents
-->

<div style="position:absolute; top:50px; left:650px; display:float;">

![center width:700px](./architecture.jpg)

<br>

<img src="./https.jpg" width="250px" />
<img src="./twitter-card.jpg" width="300px" />

</div>
<div style="position:absolute; top:30; left:10;">

## この構成で実現できること

### 独自ドメイン
Route53での tomtsutom.com の取得

### 常時SSL化

- ACMによるSSL証明書の取得
- CloudFrontでhttpからhttpsへリダイレクト

### OGP対応

Lambda@Edgeを用いて実現する

</div>

---
<!--
_header: 実際にやってみた結果 - AWS編
class: contents
-->

### Route53で独自ドメイン取得

昔やったことがあったので難なくクリア

![center](./route53.jpg)

---
<!--
_header: 実際にやってみた結果 - AWS編
class: contents
-->

### SSL証明書の取得・設定
ACMのSSL証明書はus-east-1で作る必要があるのに間違えた
(この理由を突き止めるだけで3時間くらいは潰した… 🙄)

![center width:700px](./acm-cert.jpg)

---
<!--
_header: 実際にやってみた結果 - AWS編
class: contents
-->

### CloudFrontのデフォルトキャッシュが1日で長過ぎる

<div style="display: flex; justify-content: center; align-items: center;">
<div style="width: 50%; text-align: center">

![left w:350px](./cache-86400.jpg)
デフォルト

</div>
<div style="width: 50%; text-align: center">

![left w:350px](./cache-60.jpg)
キャッシュを1分に設定した場合

</div>
</div>

![center width:520px](./architecture.jpg)

---
<!--
_header: 実際にやってみた結果 - AWS編
class: contents
-->

## OGP対応(Twitterでサイトの情報が表示されるやつ)
Lambda@Edgeでサーバーレスの弱点を克服！

## Lambda@Edgeのここがすごい

<div style="display: flex">
<div style="width: 50%">

- リクエストに対して柔軟に対応できる
- 料金はコードが実行された分だけ
- Edgeで動くのでレスポンスが早い

</div>
<div style="width: 50%">

![width:100%](./LambdaEdge.jpg)

</div>
</div>

---
<!--
_header: 実際にやってみた結果 - AWS編
class: contents
-->

## OGP対応

SSRをやめる。OGP対応はLambda@Edgeでダイナミックレンダリングする。https://qiita.com/geerpm/items/78e2b85dca3cb698e98d を参考にして作った。

### 原理

ブラウザをLambda上で動かして、HTMLのコードを生成している。

しかし、ブラウザバージョンが古いなどの理由で色々動かなかった

### 結果

試行錯誤をした結果できたコードが以下の通り
https://github.com/Tsutomu-Ikeda/origin-request-crawler
https://github.com/Tsutomu-Ikeda/viewer-request-fixer

---
<!--
_header: 実際にやってみた結果 - AWS編
class: contents
-->

## かかった費用は一ヶ月あたり $1.58 $\approx$ 169円(2020年7月現在)

### ドメイン取得費用 $12.00 / 年
### Route 53使用料 $0.50 / 月
### CloudFront使用料 $0.03 / 月
Lambda@Edgeの料金
### S3使用料 $0.00 / 月
転送量があまり多くないので安い
### 消費税 $0.05 / 月

---
<!--
_header: どうやったか - フロントエンド編
class: contents
-->

# React + TypeScript + Material UI

## TypeScript

Visual Studio Code でコード補完が出る！

## レスポンシブデザイン

## GitHubで管理

---
<!--
_header: どうやったか - フロントエンド編
class: contents
-->

## GitHubで管理

GitHub Actionsを用いてコミット後にS3へアップロードするように設定した

![](./GitHubActions.jpg)

ビルドして、S3のコンソールを開き、アップロードするというタスクを自動化できた！

---
<!--
_header: 実際にやってみた結果 - フロントエンド編
-->

## Google PageSpeed Insightsを用いて計測した

![center w:500px](PageSpeedInsights-73.jpg)

---
<!--
_header: 実際にやってみた結果 - フロントエンド編
-->

# 73点は悪くないけど改善の余地あり！

## 具体的に指摘された内容
- 使わないJavaScriptのコードが読み込まれている
- ページを開いた瞬間の画像読み込み枚数が多い

---
<!--
_header: 実際にやってみた結果 - フロントエンド編
-->

## JavaScriptの分割
これはめっちゃ簡単

![](./LazyJavascript.jpg)

import文を関数にする。本当に必要なとき(関数が呼ばれたときだけ)importされる。

実装したコミット: [8292305](https://github.com/Tsutomu-Ikeda/tomtsutom.com/commit/829230597ce607792f00d9cd1c4f214dcbbe72d7)

---
<!--
_header: 実際にやってみた結果 - フロントエンド編
-->

## 画像の遅延ロードを実装

必要なときまで画像を読み込まないようにした

![center width:550px](./LadyImage.jpg)

実装したコミット[bd16f9e](https://github.com/Tsutomu-Ikeda/tomtsutom.com/commit/bd16f9e39549e7006fbd6d7fa325bda9a7c08c53)

---
<!--
_header: 実際にやってみた結果 - フロントエンド編
-->

## その結果ほぼ満点になった！

![center width:900px](PageSpeedInsights.jpg)

---
<!--
_header: まとめ
-->

## AWSをフル活用すると格安でサイトが作れる

その額 169円/月

## コードをチューニングすると爆速サイトが作れる

73点 → 97点

# みなさんもぜひ自分のページを作ってみてください！

https://tomtsutom.com
https://github.com/Tsutomu-Ikeda/tomtsutom.com
