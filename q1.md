# npm コマンドの実行ファイル確認

```bash
$ which npm
/home/user/.nvm/versions/node/v23.3.0/bin/npm

$ type npm
npm is hashed (/home/user/.nvm/versions/node/v23.3.0/bin/npm)

$ command -v npm
/home/user/.nvm/versions/node/v23.3.0/bin/npm
```

bash
npm コマンドの実行ファイルを調べた結果、上記の通りでした。

```bash
~/.nvm/versions/node/v23.3.0/bin$ cat npm
#!/usr/bin/env node
require('../lib/cli.js')(process)
```

npm コマンドの実行ファイルの中身は、上記の通りでした。

```
~/.nvm/versions/node/v23.3.0/lib$ ls
node_modules
```

../lib/cli.js を探して lib ディレクトリ内を参照したところ、node_modules ディレクトリしか存在していません。

# 疑問

この lib 配下に cli.js が存在していないのにも関わらず、なぜ npm コマンドは正常に動作できているのでしょうか？

[package.json](https://github.com/npm/cli/blob/cf52b8be2645ee098ee83ea9981fc32a11932fad/package.json#L39) には、npm cli のエントリーポイントは `index.js` と書いてあります。<br>
実際、リポジトリを `clone` して `node index.js <command>`を実行するとちゃんと動作するし、コードを読む限りもエントリーポイントとして適切なファイルであると思えます。<br>
しかし、npm 実行ファイルの中には、`index.js` を実行する指示が書かれていません。<br>
試しに下記の通り、`/.nvm/versions/node/v23.3.0/lib/node_modules/npm/index.js` のなかに `console.log` を試しに入れてみましたが、npm コマンドを叩いても`"wwww"`は出力されませんでした。<br>

```
if (require.main === module) {
console.log("wwwww")
require('./lib/cli.js')(process)
} else {
throw new Error('The programmatic API was removed in npm v8.0.0')
}
```

npm cli がローカルで実行されるときの、実際のエントリーポイントは一体どこになるのでしょうか？
