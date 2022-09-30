> 引用

---
# title: JavaScriptで動的に作成したテキストファイルをダウンロード

# 概要

JavaScriptでブラウザのメモリの内容からテキストファイルを生成してダウンロードさせます。
次の２つの手順に分けます。

1. URLを作る
2. ダウンロードする

# URLを作る

ダウンロード用のURLを作る方法は２つあります。

## data URIs


[data URIs](https://developer.mozilla.org/ja/docs/data_URIs)でコンテンツタイプとコンテンツを指定したURLスキームを作ります。
例えば：

```
data:,hello
```

です。

### エスケープ

直接文字列を指定した場合、改行が無視されます。整形したJSONファイルを作る際は[encodeURIComponent](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/encodeURIComponent)を使ってエンコードします。
例えば：

```js
`data:,${encodeURIComponent(JSON.stringify(data, null, 2))}`
```


## [Blob URLs](https://www.w3.org/TR/FileAPI/#creating-revoking)（HTML5）

[Blobオブジェクト](https://developer.mozilla.org/ja/docs/Web/API/Blob)と[URL.createObjectURL](https://developer.mozilla.org/ja/docs/Web/API/URL/createObjectURL)を使ってURLを作ります。
例えば：

```js
URL.createObjectURL(new Blob(['hello'], {
  type: "text/plain"
})
```

## まとめ

生成する文字列が短い場合は`data URIs`を使いましょう。
長文をurlエンコードすると、サイズが大きくなることがあります。Blob URLsを使いましょう。

# ダウンロードする

## [window.location](https://developer.mozilla.org/ja/docs/Web/API/Window/location)

`location.href`に上で作成したURLを代入するとダウンロードが開始されます。

### data URIs

例えば

```js
location.href = 'data:text/plain;charset=UTF-8,hello'
```

です。

#### GoogleChromeでの制約

GoogleChrome(Version 60.0.3112.101)で、window.locationを使ってダウンロードしようとすると以下のエラーが出ます。

![Screen Shot 2017-08-25 at 16.04.41.png](https://qiita-image-store.s3.amazonaws.com/0/15964/2ca7ac9f-39fa-6abb-f1f5-1665fb34e577.png "Screen Shot 2017-08-25 at 16.04.41.png")

> Not allowed to navigate top frame to data URL: data:text/plain;charset=UTF-8,hello

これを回避するには

[Intent to Deprecate and Remove: Top-frame navigations to data URLs - Google グループ](https://groups.google.com/a/chromium.org/forum/#!topic/blink-dev/GbVcuwg_QjM) によると

- non-browser-handled MIME types
- download属性

のいずれかを使います。

`non-browser-handled MIME types`を使う場合は、MIME typeに`application/octet-stream`を指定します。
例えば

```js
location.href = 'data:application/octet-stream, hello'
```

です。この場合でも

![Screen Shot 2017-08-25 at 16.05.54.png](https://qiita-image-store.s3.amazonaws.com/0/15964/b87395f5-3ddf-a6f9-f168-4cc1927b9305.png "Screen Shot 2017-08-25 at 16.05.54.png")


> Resource interpreted as Document but transferred with MIME type application/octet-stream: "data:application/octet-stream, hello".

とWarningが表示されます。

download属性を使う方法は後述します。


#### FirefoxとSafariでの制約

Firefox(54.0.2), Safari(Version 10.1.1 (12603.2.4))では、MIME typeがdefault(text/plan)の場合、ダウンロードされずに遷移します。
MIME typeに`application/octet-stream`を指定すれば、ダウンロードが開始されます。

### blob URLs

例えば：

```js
location.href = URL.createObjectURL(new Blob(['hello'], {
  type: "text/plain"
}))
```

#### GoogleChromeでの制約

GoogleChrome(Version 60.0.3112.101)では、MIME typeがdefault(text/plan)の場合、ダウンロードされずに遷移します。
MIME typeに`application/octet-stream`を指定すれば、ダウンロードが開始されます。

```js
location.href = URL.createObjectURL(new Blob(['hello'], {
  type: "application/octet-stream"
}))
```

この場合でも

![Screen Shot 2017-08-26 at 10.42.57.png](https://qiita-image-store.s3.amazonaws.com/0/15964/12a9129b-8205-8ac4-c6b0-b16c92b24b8e.png "Screen Shot 2017-08-26 at 10.42.57.png")


> Resource interpreted as Document but transferred with MIME type application/octet-stream: "blob:null/30e18e5d-120f-41e5-837b-b4f015e94ae0".      

とWarningが表示されます。

#### FirefoxとSafariでの制約

Firefox(54.0.2), Safari(Version 10.1.1 (12603.2.4))では、MIME typeがtext/planの場合、ダウンロードされずに遷移します。
MIME typeに`application/octet-stream`を指定すれば、ダウンロードが開始されます。


## aタグ download属性（HTML5）

HTML5ではaタグにdownload属性が追加されました。
download属性を指定すると

1. ページ遷移がファイルダウンロードに
2. ダウンロードするファイル名を指定できる

例えば：

```html
<a id="link1" download="hello.txt" href="data:,hello">Data Scheme URL</a>
```

動的に作成したファイルを設定する場合は、aタグのclickイベントハンドラーで`href`属性を書き換えます。
例えば：

```js
document
  .querySelector('a')
  .addEventListener('click', (e) => e.target.href = `data:application/json;charset=UTF-8,${JSON.stringify({massage:"hello"})}`)
```

Blob Scheme URLを使うこともできます。例えば：

```js
document
  .querySelector('a')
  .addEventListener('click', (e) => e.target.href = URL.createObjectURL(new Blob(['hello'], {
    type: "text/plain"
})))
```

リンククリック時のブラウザの遷移動作はイベントハンドラーの後に実行されます。

## まとめ

- マルチブラウザ対応
- ファイル名を指定したい

ならば、download属性を使いましょう。




# サンプルコード

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>text file download</title>
</head>

<body>
    <article>
        <h1>location.href</h1>
        <button id="button1">data URIs(With default MIME type)</button>
        <button id="button2">data URIs(With MIME type application/octet-stream)</button>
        <button id="button3">Blob URLs(text/plain)</button>
        <button id="button4">Blob URLs(application/octet-stream)</button>
    </article>
    <article>
        <h1>a tag download attribute</h1>
        <a id="link1" download="hello.txt" href="data:,hello">data URIs(text)</a>
        <a id="link2" download="hello.json" href="data:,hello">data URIs(JSON)</a>
        <a id="link3" download="hello.txt" href="#">Blob URLs(text)</a>
        <a id="link4" download="hello.json" href="#">Blob URLs(JSON)</a>
    </article>
    <script type="text/javascript">
        const data = {
            massage: "hello",
            detail: {
                count: 1,
                isError: false,
                content: 'ワールド'
            }
        }

        document.querySelector('#button1').addEventListener('click', () => location.href = 'data:,hello')
        document.querySelector('#button2').addEventListener('click', () => location.href = 'data:application/octet-stream, hello')
        document.querySelector('#button3').addEventListener('click', () => location.href = URL.createObjectURL(new Blob(['hello'], {
            type: "text/plain"
        })))
        document.querySelector('#button4').addEventListener('click', () => location.href = URL.createObjectURL(new Blob(['hello'], {
            type: "application/octet-stream"
        })))
        document.querySelector('#link2').addEventListener('click', (e) => {
            e.target.href = `data:,${encodeURIComponent(JSON.stringify(data, null, 2))}`
        })
        document.querySelector('#link3').addEventListener('click', (e) => e.target.href = URL.createObjectURL(new Blob(['hello'], {
            type: "text/plain"
        })))
        document.querySelector('#link4').addEventListener('click', (e) => e.target.href = URL.createObjectURL(new Blob([JSON.stringify(data, null, 2)], {
            type: "text/plain"
        })))
    </script>
</body>

</html>
```



# 参考
- [JavaScriptプログラミング講座【Data URI Scheme について】](http://hakuhin.jp/js/data_uri_scheme.html)
- [JavaScriptプログラミング講座【Blob URL Scheme について】](http://hakuhin.jp/js/blob_url_scheme.html)
- [JavaScriptでJSONファイルダウンロード - Qiita](http://qiita.com/ganezasan/items/b5dbb42b966af0de44cb)
- [JavaScriptでファイルダウンロード処理を実現する - Qiita](http://qiita.com/wadahiro/items/eb50ac6bbe2e18cf8813)
- [ボタンがクリックされたらファイルをダウンロードする | 主にjQueryのメモ](https://elearn.jp/jmemo/javascript/memo-325.html)

