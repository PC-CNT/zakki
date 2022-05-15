# Deno

## Denoってなに

>*Deno is a simple, modern and secure runtime for JavaScript, TypeScript, and WebAssembly that uses V8 and is built in Rust.* - [公式サイト](https://deno.land/)より

要するにローカルでJavaScriptが動かせるやつです[^1]

ついでに素でTypeScriptも動くとのこと

Node_modulesやPackage.jsonの概念がないため取っつきやすい

## 導入

[公式](https://deno.land/#installation)に書いてある通りにコマンドを入力します

今回はWindowsなのでpowershellで以下のコマンドを実行しました

```powershell
Invoke-WebRequest https://deno.land/install.ps1 -UseBasicParsing | Invoke-Expression
```

インスコに成功してたら`Deno help`で情報がいっぱい出てくるはず

## Visual Studio Code

1. [Denoの拡張機能](https://marketplace.visualstudio.com/items?itemName=denoland.vscode-deno)をインスコ
2. 設定から`deno.path`にDeno.exeのパスを記述
3. 設定から`deno.enable`を有効にする
4. `Ctrl+Shift+P` でコマンドパレットを開いて `deno.initializeWorkspace` を実行

これでDeno language serverが起動する（はず）

deno-lintが素のjs/tsでもESLintみたいな働きをしてくれるので結構便利[^2]

## 実際に書いてみる

### Hello World

hello.ts

```ts
console.log("Hello World!");
```

`deno run ファイル名` でjs/tsを実行

```cmd
C:\Users\user>deno run hello.ts
Check file:///C:/Users/user/hello.ts
Hello World!
```

`deno compile ファイル名` でexe化できるらしい

```cmd
C:\Users\user>deno compile hello.ts
Check file:///C:/Users/user/hello.ts
Compile file:///C:/Users/user/hello.ts
Emit hello.exe

C:\Users\user>C:\Users\user\hello.exe
Hello World!
```

↑できた

[^1]: 多分この認識は間違ってる

[^2]: ESLint使ったことないからよくわからんけど多分ESLintの方がいいと思う
