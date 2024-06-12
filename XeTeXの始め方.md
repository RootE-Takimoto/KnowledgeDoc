# XeTeXの始め方

## Advancedな環境構築(XeTeXを使う)

ゴリゴリにデバイス環境に依存するかわりにフォント設定などを簡単化したのがXeTeX。
あととにかくビルドが爆速。これもClutTeXで設定。個人的にはおすすめです。
日本語環境で小文字の行頭禁則あたり（行頭にかな小文字が来ないようにする設定）に若干の難があり、
禁則処理を設定しないか、設定する場合は多少の工夫が必要になることは問題です。
それにつけてもとにかくビルドが早いので、最低限のテンプレートとパッケージでプロットを上げる様なときには最適です。
そして欧文を書くなら何も問題が出ないでしょう。

## 和文組版のための設定

### 必須プリアンブル周り

兎に角大事なことは、bxjscls系のクラスを使うことと、zxjafont,zxjatype,xeCJKの各パッケージをプリアンブルに記述しておくことです。

最低限のプリアンブル周りだけここに書いておきます。

```latex
\documentclass[xelatex,ja=standard,a4paper,11pt,prefercjk]{bxjsreport}
\usepackage[AutoFakeSlant=0.2]{xeCJK}
\usepackage{zxjatype}
\usepackage[haranoaji]{zxjafont}
```

bxjscls系のドキュメントクラスを使う場合、```jafont=```オプションでzxjafontのプリセット指定を済ませられますが、ここでは使わず、zxjafontのオプションで指定してください。
```jafont=```で指定した場合、xeCJKのオプションAutoFakeSlantが効かなくなります。
このオプションは、斜体が存在しない日本語フォントを無理矢理斜体にしてくれるオプションで、これが効いていると、和文でもイタリック```\textit{}```が効くようになります。
なお、このオプションを効かせる都合上、プリセットで定まっているフォントを再定義することになるため、xeCJKからRedefining...のようなWarningが二つほど（明朝とゴシックの分）出ますが、これは意図してやっているが故のWarningなので無視してください。

### フォント設定

zxjafontのプリセットを使うのが簡単ですが、それ以外にも使いたい場合があるでしょう。というかXeTeX使い始めたんだからそういうことはもちろんありますよね。
とりあえず使いたいフォントをシステムにインストールしてください。Winとかなら適当にotfなんかをインストールポチッとすれば良いですね。WSLを使ってたりする場合はそのLinuxにインストールするんですよ!（1敗）

xeCJKがあれば基本的に和文のフォント設定は出来ます。欧文はfontspecを使います。

さて、では環境構築。こちらもClutTeXを使います。
ビルドツールに

```json
        {
            "name": "cluttex(xelatex+biber) --fresh",
            "command": "cluttex",
            "args": [
                "--engine=xelatex",
                "--max-iterations=1",
                "--fresh",
                "-synctex=1",
                "--biber",
                "%DOC%"
            ]
        },
```

```json
        {
            "name": "xetex",
            "command": "xelatex",
            "args": [
                "-file-line-error",
                "-synctex=1",
                "-interaction=nonstopmode",
                "-halt-on-error",
                "%DOC%"
            ],
        },
```

を追記して
レシピに

```json
        {
             "name": "ClutTeX(XeLaTeX+Biber)",
             "tools":[
                 "cluttex(xelatex+biber) --fresh"
             ]
         },

```

を追加すればOK。
なんかしらんけど中間ファイルのauxファイルがエラー履歴を更新しないので、出力は正常なのに一回出た参照エラーがずっと消えないみたいなことが起きるので、一回 --fresh を挟んで前歴を殺してます。よっぽどauxが消えてほしくない場合以外はこれでよろしいかと。
