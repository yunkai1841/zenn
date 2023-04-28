---
title: "vscodeでFORTRANを快適に開発したい"
emoji: "🌝"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["fortran", "vscode"]
published: true
---

FORTRAN77を触ることになりましたが、vscodeのデフォルトで`*.f`ファイルを開いても、何もサポート機能が効きません。これではせっかくのvscodeただのメモ帳。少し触るくらいならそれでも良いのですが、FORTRANは古くて、インデントが少しでも狂うとコンパイルが通らない上に、どこにエラーがあるかも非常に分かりにくいです。
そこで、vscodeでFORTRANを快適に開発するための設定をしましたので、紹介します。現代的なツールで、楽に開発したいですね。

# 拡張機能のインストール

Modern Fortranという拡張機能をインストールします。
https://marketplace.visualstudio.com/items?itemName=fortran-lang.linter-gfortran

# 必要なパッケージのインストール

この拡張機能を使うには、`fortls`と`findent`が必要ですので、インストールします。これらは、PyPIからインストールできます。グローバルにインストールしたくない場合は、`--user`オプションをつけてください。

```bash
pip install fortls findent
```

ちなみに、これらはそれぞれlanguage-serverと整形ツールであり、ほかのパッケージでも代用できました。詳しくはModern FortranのREADMEを参照してください。

# 設定

vscodeの設定をしましょう。
まず、FORTRANの仕様上、行頭のインデントは必ずスペース6個にする必要があります。しかし、一行の長さは72文字までと決まっているので、ループ文など普通のインデントはスペース3個にしたいです。
そこで、ワークスペース内の設定をそれぞれ以下のように変更しました。jsonファイルを直接編集するほうが速いのでおすすめします。

![detect indent](/images/fortran-vscode/settings-detect.png)  
![tab size](/images/fortran-vscode/settings-tabsize.png))  


```json:.vscode/settings.json
{
   "editor.detectIndentation": false,
   "editor.tabSize": 6,
   "editor.indentSize": 3,
}
```

:::details パスが通っていない場合
パッケージインストールでインストールした`fortls`と`findent`のにPATHが通っていない場合は、手動で設定する必要があります。fortran.fortls.pathとfortran.formatting.pathを設定してください。

```json:.vscode/settings.json
{
   "fortran.fortls.path": "/home/username/.local/bin/fortls",
   "fortran.formatting.path": "/home/username/.local/bin/findent",
}
```
:::

これで、FORTRANの開発が快適になりました！！！
Alt+Shift+Fで自動整形！
どこにエラーがあるかも表示してくれるようになりました。