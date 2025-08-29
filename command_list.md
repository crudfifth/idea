了解。技術的なポイントだけ、シンプルに確認＆補足します。

VITE_ENV の件

認識合っています。クライアントから参照する環境値は VITE_ プレフィックスを付けて .env.[mode] に書きます。コード側は import.meta.env.VITE_ENV で参照します。

NODE_ENV は Vite/ツールチェーン内部向け（development/production/test の3値想定）なので、触らない／使わないでOK。

mode の名称

dev/mnt/stg/prd のような任意文字列に変更可能です。.env.dev / .env.mnt / .env.stg / .env.prd を用意してください。

CIのビルド

各環境用は vite build --mode <env>（例：--mode stg）。これで該当 .env.<env> が読み込まれます。

DEPLOYMENT_ENV など独自の環境変数は、クライアントで使うなら VITE_ を付ける（例：VITE_DEPLOYMENT_ENV）。付けないとクライアントからは見えません。

preview

preview はビルド済み dist を配信するだけなので、--mode 指定は不要です。vite build --mode stg → vite preview の順でOK。

以上。間違いは特にありませんが、もし preview --mode ... としていたらそこだけ外してください。
調査用コマンド
Invoke-WebRequest -Uri "(例)https://perplexity.ai" -Method Head

PS4 米山 貴裕/Yoneyama, Takahiro (NTT DATA)へ山浦から質問
一応確認させてください。
↓の DEPLOYMENT_ENVは、デフォルトdevelopmentとしますが、
ビルドパイプラインで、prd/stg/mnt用のdistが利用できるように書き換える想定、で合ってます？
 
あと、modeが現状development, production, testになっていると思います。
↓ような環境識別子の形式に変更したいのですが可能ですか？
＃dev, mnt, stg, prd
 
PS4 山浦 将貴/Yamaura, Masaki (NTT DATA)へ米山回答
>一応確認させてください。
>↓の DEPLOYMENT_ENVは、デフォルトdevelopmentとしますが、
>ビルドパイプラインで、prd/stg/mnt用のdistが利用できるように書き換える想定、で合ってます？
認識合います。ビルドパイプラインでDEPLOYMENT_ENVを設定しなかったらdevelopmentになりますが、設定した場合、その内容で上書きされます。
ubuntu環境でdevとstgでそれぞれビルドされたdistが使われていることは確認してます。（証跡としては残っていないので残します。）
 
>あと、modeが現状development, production, testになっていると思います。
>↓ような環境識別子の形式に変更したいのですが可能ですか？
>＃dev, mnt, stg, prd
はい、可能です。
modeオプションは指定された値と一致する「.env.[mode]」ファイルを参照するため、ファイル名を変更するだけで問題ないと思います。

例）.env.development → .env.dev
 
また、package.jsonのpreviewコマンドも同様に変更が必要です。

"preview:development": "vite preview --config config/vite.config.ts --mode development"

→"preview:dev": "vite preview --config config/vite.config.ts --mode dev"
 
以下のように各envファイル内のNODE_ENVでもdevelopmentのように環境を指定していますが、内部処理等で使用される重要な値のため変更不要なようです。
 
NODE_ENVの修正が必要かを調べたところ、NODE_ENVには「development/production/test」の3つの値しか設定できないことが分かりました。そのため「.env.staging」の「NODE_ENV=staging」は間違っていました。
Vueの各種機能が正しく動作しない可能性があるようなので、以下のように修正します。
NODE_ENV=staging → NODE_ENV=production
 
また、現状は3環境（development, staging, production）しかenvファイルがないため、mnt環境用の設定を新規作成する必要があります。設定内容は以下で問題ないですか？
VITE_ENV=production

VITE_REMOVE_CONSOLE=true
 
PS4 髙橋 綱弥/Takahashi, Tsunami (NTT DATA)（私）へ米山からの質問
modeの指定について、変更箇所の認識合いますでしょうか？
 
