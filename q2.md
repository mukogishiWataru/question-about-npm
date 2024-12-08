npm.jsファイルの[load()](https://github.com/npm/cli/blob/cf52b8be2645ee098ee83ea9981fc32a11932fad/lib/npm.js#L80)は、this.#load()を実行して、最終的に入力されたコマンドをreturnしています。そのthis.#load()を呼び出すときの書き方に疑問があります。<br>

```
const { log, time, output, META } = require('proc-log')

return await time.start('npm:load', () => this.#load())
```

単に`this.#load()`と記述するでも充分なはずだと思いますが、npm cliではたびたびこの書き方が見受けられます。<br>
何のためなのか気になり、[proc-logの実装](https://github.com/npm/proc-log/blob/8105dea8ca31296f5826e62683e63539401d6a8e/lib/index.js#L95)を確認したところ、以下の通りでした。

```
    start: function (name, fn) {
      process.emit('time', 'start', name)
      function end () {
        return process.emit('time', 'end', name)
      }
      if (typeof fn === 'function') {
        const res = fn()
        if (res && res.finally) {
          return res.finally(end)
        }
        end()
        return res
      }
      return end
    },
```

`time.start`の中身としては、おそらくここだけで完結しているかと思います。<br>

`process.emit`で、`'time'`イベントに登録されているイベントリスナーを呼び出しています。ですがそのためには、事前に`process.on('time', () => {})`でイベントリスナーを登録しておく必要があるのに、その記述はありません。<br>

一体何のために、このような書き方をしているのか、何か見当つきますでしょうか？（何がやりたいのかがさっぱりわかりません・・）
