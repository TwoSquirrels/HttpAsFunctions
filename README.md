# HTTP as Functions

> [!Warning]
> このプロジェクトはアーカイブされており、メンテナンスされていません。
> 作者がプログラミング初心者の頃（約4年前）に作成したライブラリであり、学習用または「アンチパターン」の事例として残されています。実運用環境では使用しないでください。

# What is this?

HTTP as Functions 略して HaF は、  
HTTP通信を関数の用に扱えるライブラリです。  
これを使えばネットワーク間やプロセス間(通信先をlocalhostにする)で関数を呼び出すことができます。  
引数と戻り値は [Gson](https://github.com/google/gson) のJsonElementとなっています。  
関数はライブラリを通して登録したり呼び出したりする事ができますが、中では  
登録は [Spark Freamwork](https://sparkjava.com/) を用いて鯖を建て
呼び出しは [OkHttp](https://square.github.io/okhttp/) を用いて鯖にPOSTリクエストを送信しているだけです。

## ⚠️ Known Issues / Post-mortem

このライブラリには、設計および実装において以下のような重大な問題点が含まれています。

### 設計上の課題

* **車輪の再発明**: JSONを用いたRPC（遠隔手続き呼び出し）を独自実装していますが、既存の標準規格である **JSON-RPC 2.0** を採用すべきでした。
* **HTTPセマンティクスの無視**: エラー発生時（バリデーションエラーや内部例外）でも、HTTPステータスコードとして常に `200 OK` を返却しています。本来は `4xx` や `5xx` を適切に使用し、クライアントが標準的なエラーハンドリングを行えるように設計すべきです。
* **終了処理の欠如**: 起動したサーバーを停止させるメソッドが実装されていません。これにより、リソースリークやポートの競合が発生する可能性があります。

### 実装・Javaの作法に関する問題

* **継承の誤用**: `StatusMessages` クラスが `HashMap` を継承していますが、これは不適切な継承です。コンポジションを使用するか、定数として定義すべきでした。
* **スレッドセーフ性の欠如**: サーバーインスタンスを管理する `Map` がスレッドセーフではないため、並行処理時に不具合が生じる可能性があります。
* **過剰な依存関係**: 単一の機能を提供するために、フルスタックなWebフレームワーク（Spark Java）に依存しており、ライブラリとして重量過多（オーバーキル）です。
* **冗長なラッパー**: `JsonObjectArgs` クラスを作成していますが、Gsonの標準機能で十分であり、コードの複雑性を不必要に高めています。
* **例外の握りつぶし**: 通信エラーなどの例外を catch ブロック内で無視している箇所があり、デバッグを困難にしています。

# How to use

## 1. Dependencies

### raw

- repository: `https://raw.githubusercontent.com/TwoSquirrels/HttpAsFunctions/main/repository`
- gropuId: `io.github.twosquirrels`
- artifactId: `httpasfunctions`
- version: `1.3.0`

### maven

```xml
<project ...>

  ...

  <dependencies>

    ...

    <!-- https://github.com/TwoSquirrels/HttpAsFunctions -->
    <dependency>
      <groupId>io.github.twosquirrels</groupId>
      <artifactId>httpasfunctions</artifactId>
      <version>1.3.0</version>
    </dependency>

  </dependencies>

  ...

  <repositories>

    ...

    <!-- https://github.com/TwoSquirrels/HttpAsFunctions -->
    <repository>
      <id>twosquirrels-haf</id>
      <name>Repository for Http as Functions</name>
      <url>https://raw.githubusercontent.com/TwoSquirrels/HttpAsFunctions/main/repository</url>
    </repository>

  </repositories>

  ...

</project>
```

### gradle

Sorry, I do not understand.

## 2. Write your code!

### document

[Javadoc](https://twosquirrels.github.io/HttpAsFunctions/1.3.0/javadoc/)

ドキュメント書くのだるそう  
とりあえずJavadocみるなり僕に聞くなりして使ってちょ

### example

いつか書く

# When use it

## プロセス間

- マイクラのプラグイン同士でデータの送信、取得をしたい時

## ネットワーク間

- ソフトやゲームでサーバーにデータを送信したりサーバーからデータを取得したい時
