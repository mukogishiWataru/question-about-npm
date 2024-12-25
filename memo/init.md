`init.js`で、呼び出すべきパッケージ名の変換が行われる。`vue@latest`が`create-vue@latest`になる。<br>
`init.js`のexec関数のなかでexecCreate関数が呼ばれる。<br>
`npa(npm-package-arg)`ライブラリで、引数である`vue@latest`を解析して以下のオブジェクトを取得。<br>

```
Result {
  type: 'tag',
  registry: true,
  where: undefined,
  raw: 'vue@latest',
  name: 'vue',
  escapedName: 'vue',
  scope: undefined,
  rawSpec: 'latest',
  saveSpec: null,
  fetchSpec: 'latest',
  gitRange: undefined,
  gitCommittish: undefined,
  gitSubdir: undefined,
  hosted: undefined
}
```
`vue@latest`がnpmレジストリにホストされているなら、registry: trueになる。<br>
req.nameが"vue"、req.rawSpecが'latest'。packageNameは`create-vue@latest`になる。

```
else if (req.registry) {
        packageName = `${req.name.replace(/^(@[^/]+\/)?/, '$1create-')}@${req.rawSpec}`
      } 

 const newArgs = [packageName, ...otherArgs]
```

```
    await libexec({
      ...flatOptions, //config色々入ってる。プロパティ数110個。
      args: newArgs, // [ 'create-vue@latest' ]
      localBin, //'/home/user/<カレントディレクトリ>/node_modules/.bin'
      globalBin, //'/home/user/.nvm/versions/node/v23.3.0/bin'
      output, //ロギングの設定関連のオブジェクト
      chalk, //ターミナルで色を使うための関数
      path: this.npm.localPrefix, //カレントディレクトリ
      runPath, //カレントディレクトリ
      scriptShell, //undefined (受け取った側でwindowsのシェルかどうか判定するのに使う)
      yes, //null　（パッケージ取得時の確認プロンプトをスキップするか）
    })
```

npm cliで明示的に書いてあるのは、コマンドライン引数を解析して、libnpmexecに渡して実行させること。