【私回答】
ご指摘ありがとうございます。確認しましたが、結論としてはご認識のとおりです。  
`server.use(...)` は Node 用の msw（msw/node）向けの API であり、ブラウザ環境で動作する Playwright の mock-e2e では利用できません。  
そのため、Playwright でのハンドラ切り替えは `mocks/server.ts` ではなく `mocks/browser.ts` を起点に起動している Service Worker に対して行う必要があります。  

なお、サンプルブランチで `mocks` 配下と `stubs` 配下の中身が同一になっていたのは、単にデータを重複して配置してしまっていたためです。切り替え機能自体が無効というわけではなく、今後は `stubs` に統一して管理する方針です。  

開発ガイドラインについては、「ユニットテスト（Vitest等）では `server.use(...)` を使うが、mock-e2e／VRT はブラウザ側の `worker.use(...)` を使う」という点が分かるよう、追記修正を行います。  
また、ご要望の `vitest.config.ts` の設定についても、必要に応じて develop ブランチに反映いたします。  


ご指摘ありがとうございます。確認しましたが、結論としてはご認識のとおりです。  
`server.use(...)` は Node 用の msw（msw/node）向けの API であり、ブラウザ環境で動作する Playwright の mock-e2e では利用できません。  
そのため、Playwright でのハンドラ切り替えは `mocks/server.ts` ではなく `mocks/browser.ts` を起点に起動している Service Worker に対して行う必要があります。  

現状サンプルブランチで `mocks` と `stubs` の中身が同じになっていたのは、単に切り替え差分を用意していなかったためで、切り替えができていないわけではありません。実際に切り替えを有効にするには、成功・失敗・空データといった異なるハンドラを `mocks/browser.ts` 側で定義して、それを Playwright のテストコードから `worker.use(...)` 経由で適用する必要があります。  

開発ガイドラインの該当箇所については、「ユニットテスト（Vitest等）では `server.use(...)` を使うが、mock-e2e／VRT はブラウザ側の `worker.use(...)` を使う」という点が分かるよう、追記修正します。  
また、ご要望の `vitest.config.ts` の設定反映についても、develop ブランチ側に適用を検討いたします。  

【AP基盤問合せ】開発ガイドラインのPlaywright記載方法について
【背景】
表題の件につきまして、Playwrightの記載方法を確認させていただきたく存じます。
開発ガイドラインのサンプルコードにて、
server.use(〇〇)を使用したハンドラの切り替え処理がございますが、
mock-e2e、vrt共にガイドの通り使用してもハンドラの切り替えが出来ない状況です。

【問合せ内容】
サンプルで作成された以下のブランチを拝見させていただきましたが、
mocksとstubsのデータの中身が同じでしたので、そちらの環境でハンドラの切り替えが出来ているか、
お手数ですが一度ご確認いただけないでしょうか。
リポジトリ：yysk-sample-client
ブランチ：feature/migrate-vrt-mocke2e-from-poc

また、勘違いでしたら申し訳ないのですが、
個人的に調べたところ、ブラウザで動いているmswにはnode用のserver.use(〇〇)が存在しないため、
Playwrightで使用できないような記載が見受けられました。
mocks/server.tsではなく、mocks/browser.tsを使用する可能性がございます。
※developブランチに追加の反映が発生する場合、
pre-commitでPlaywrightのエラーが出るので、
可能であればconfig/vitest.config.ts等への記載もdevelopブランチに反映いただけますと幸いです。

以下にこちらで作成中の実装がございます。
http://gitlab.yysk.cicd.local/business-team/yysk-client-business/-/merge_requests/99
http://gitlab.yysk.cicd.local/business-team/yysk-client-business/-/merge_requests/99

予防接種事務デジタル化に係る
予診情報・予防接種記録管理／請求支払システムの
設計・開発及び運用・保守業務一式

ソフトウェアフレームワーク機能設計書

FW_S_CLIENT_12
セッション管理




第1.1版
 
改版履歴
版数	制改訂
日付	変更
箇所	変更理由・変更内容	変更者	承認者
1.0	2025/03/24	-	初版発行	NTTD米山	NTTD 馬場
1.1	2025/05/02	-	詳細設計の反映	NTTD米山	NTTD 馬場
					
					
					
 
目次
1.	機能概要	1
1.1.	概要	1
1.2.	API一覧	1
2.	機能実現方式	1
2.1.	利用ライブラリ	1
2.2.	コンポーネント構成	1
2.3.	実現方式	2
2.4.	同一オリジンポリシー	2
3.	機能詳細	2
3.1.	API仕様	2
3.2.	プロパティ／環境変数	2
4.	利用例	3
4.1.	ストアの実装	3
4.2.	ストアで管理する状態の操作処理の実装	4
4.3.	ストアで管理する状態の初期化について	7
4.3.1.	初期化処理の実装	7
4.3.2.	ログイン時の初期化処理の実装	8
5.	その他	10
5.1.	システム基盤とのインタフェース	10
5.2.	利用するDBテーブル	10

 
 
1.	機能概要
1.1.	概要
ユーザのブラウザ上での一連の操作において画面間で引き継ぎアクセスが必要な一時保存情報を管理する機能を実現する。

1.2.	API一覧
Vue.js の状態管理ライブラリを利用する方針のため、独自の API提供は行わない。
詳細はPiniaおよびPinia Plugin Persitedstateのドキュメントを参照のこと。

2.	機能実現方式
2.1.	利用ライブラリ
本機能が利用する主なライブラリを以下に示す。

ライブラリ名	説明
pinia	Vue.js用の状態管理ライブラリ
pinia-plugin-persistedstate	Piniaの状態をブラウザに永続化するプラグイン

2.2.	コンポーネント構成
なし。

 
2.3.	実現方式
Piniaを使用してデータ管理を実装する。Piniaは、Vue.js用の状態管理ライブラリである。
Piniaを採用した主な理由は以下となる。
1.	Vue.js公式が現在推奨する状態管理ライブラリである
2.	従来の状態管理ライブラリであるVuexと比較して、シンプルな実装と型推論などのTypeScriptのサポートが充実している
3.	Vue標準機能と比較して、ストアを機能単位で分割できるため、コードの管理や修正が容易である
4.	VuexやVue標準機能と比較して、Vue.js用の開発者ツール（Vue DevTools）での状態変更の追跡やデバッグ機能が充実している
なお、Piniaのデータはメモリ上に保持されるため、画面リロード時にデータは初期化され、タブ間共有されない。そのため、pinia-plugin-persistedstateプラグインを使用してデータをWeb Storage APIのlocalStorageと同期することで、画面リロード時のデータ復元やタブ間共有を実現する。不要なセッションのデータはログイン時にストアの初期化を行う。
localStorageの詳細は Window: localStorage プロパティ を参照。

2.4.	同一オリジンポリシー
ブラウザの同一オリジンポリシーにより、localStorageで共有ができるのは共通のドメインから取得した情報に原則限る。

3.	機能詳細
3.1.	API仕様
詳細はPiniaおよびPinia Plugin Persitedstateのドキュメントを参照のこと。

3.2.	プロパティ／環境変数
なし。
4.	利用例
本機能の利用例を以下に示す。

4.1.	ストアの実装
ストアの定義の実装例を示す。
VueのOption APIと同じ方法もあるが、ここでは、Vueの実装方法と合わせてComposite APIと同じ方法で定義する例を示す。なお、Composite APIの場合は、Option APIのときに提供させる$resetメソッドがないため、リセットするメソッドは独自に実装する。
```
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'
export const useStore = defineStore('storeName', () => {
  // refで、ストアで管理する状態データ(state)を定義
  const user = ref('')
  // computedプロパティで算出データを取得する関数(getter)を定義
  const isLoggedIn = computed((): boolean => { // ログイン済みかどうかを返す算出プロパティ
    return !!user.value
  })
  // 状態を更新するアクションをfunctionで定義
  function reset() { // ストア情報をリセットするメソッド
    user.value = null
  }

  return { user, isLoggedIn, reset } // 外部から使用したいデータ、算出プロパティ、関数を記載
}, {
  persist: true  // (1)
})
```
(1).	永続化オプション。trueを設定することで、Pinia Plugin Persistedstateの機能によりブラウザのローカルストレージに保存される。
永続化オプションに関してより細かい設定が必要な場合、Pinia Plugin Persitedstateのドキュメントを参照すること。
 
4.2.	ストアで管理する状態の操作処理の実装
	ユーザ検索に関する状態管理を行うストアの設定例
```
import { defineStore } from "pinia"
import { ref } from "vue"
import type { SearchUser } from "../types/searchUsers" // 検索結果の型定義

export const useStore = defineStore("storeName", () => {
    const store = useStore(); // 自分自身のストアを取得
    // ストアで管理するデータを定義
    const userId = ref("")
    const userName = ref("")
    const email = ref("")
    const results = ref<SearchUser[]>([])

    // 同期処理メソッドを記載
    function syncLocalStorage() {
      ※次ページ参照 // (1)
    }

    // リセットメソッドを記載
    function reset() {
      store.$patch({
        userId: "",
        userName: "",
        email: "",
        results: [],
      })
    }
    return { userId, userName, email, results, syncLocalStorage, reset }
  },
  {
    persist: true, // 永続化オプション
  }
)
```
(1).	同期処理メソッドの処理について
Pinia Plugin Persistedstateは、ブラウザの更新ボタンによる再描画や、初期画面表示でのアプリケーション起動時（JavaScriptが完全に再読み込みされる時）にlocalStorageから状態を自動で復元する。

SPAでの画面遷移時は、単なるコンポーネントの切り替えであり、上記のようなJavaScriptの再読み込みは発生しないため、localStorageに対する自動的な同期は行われない。入力した内容を、別タブで表示している画面へも共有・反映したい等のケースでは、ストアが管理するデータの最新状態を反映する同期処理を実装し、明示的に呼び出す必要がある。
```
    function syncLocalStorage() {
      // localStorageから'userSearch'というキーで保存されているデータを取得。
      const savedState = localStorage.getItem("userSearch")

      if (savedState) {
        // localStorageから取得した文字列データをJavaScriptのオブジェクトに変換。
        const state = JSON.parse(savedState)

        // 全状態まとめて同期する場合の同期方法
        store.$patch({state})

        // 特定の状態を同期する場合の同期方法
        store.$patch({
          userId: state.userId || "",
          userName: state.userName || "",
          email: state.email || "",
        })
      }
    }

```
 
	ユーザ検索画面の<script>部分の実装例
```
import { useUserSearchStore } from "../stores/userSearch" // ストアのインポート

const userSearchStore = useUserSearchStore() // ストアのインスタンスを生成
userSearchStore.syncLocalStorage() // ストアの同期処理を実行

// (1)
userSearchStore.userId = "user1"
userSearchStore.results = [
  { userId: "user1", userName: "山田太郎", email: "yamada@example.com" },
  { userId: "user2", userName: "鈴木一郎", email: "suzuki@example.com" },
]

// (2)
const storeUserId = userSearchStore.userId
const displayResults = userSearchStore.results
```
(1).	ストアで管理している状態を更新。
(2).	ストアで管理している状態を参照。
 
4.3.	ストアで管理する状態の初期化について
4.3.1.	初期化処理の実装
不要なセッションのデータはストアの初期化を行う。初期化処理は各ストアで定義する。
	登録完了画面の<script>部分の実装例
```
import { useRegisterUserStore } from "../stores/registerUser"

const store = useRegisterUserStore()

store.reset() // (1)
```
(1).	ストアで定義した初期化メソッドを使用して、ストア内のすべての状態を初期化する。
 
4.3.2.	ログイン時の初期化処理の実装
ストアは永続化されているため、ログイン時に前回ログイン時の引き継ぎ不要なデータ（表示するページ数など）を全て初期化する必要がある。そのため、業務単位でストアを一括で初期化するモジュールを作成し、ログイン時に実行する必要がある。

1.	ストア初期化モジュールの実装例
```
// (1)
import { useRecipientStore } from "../stores/recipientStore"
import { useQuestionnaireStore } from "../stores/questionnaireStore"
import { useRecordStore } from "../stores/recordStore"

// (2)
export const resetVaccinationStores = () => {
  // (3)
  const recipientStore = useRecipientStore()
  const questionnaireStore = useQuestionnaireStore()
  const recordStore = useRecordStore()

  // (4)
  recipientStore.reset()
  questionnaireStore.reset()
  recordStore.reset()
}
```
(1).	初期化が必要なストアをインポート。
(2).	リセット用の関数を定義。
(3).	各ストアのインスタンスを取得。
(4).	各ストアで定義した初期化処理を実施。
 
2.	ログイン処理の<script>部分の実装例
```
// (1)
import { resetVaccinationStores } from "../utils/resetVaccinationStores"
import { resetBillingStores } from "../utils/resetBillingStores"

const login = async () => {
  try {
    // ログイン処理

    // (2)
    resetVaccinationStores()
    resetBillingStores()

    // ログイン後の画面遷移
  } catch (error) {
    // エラー処理
  }
}
```
(1).	業務単位でモジュール化した初期化処理をインポート。
(2).	ストア初期化関数を実行。
 
5.	その他
5.1.	システム基盤とのインタフェース
なし。
5.2.	利用するDBテーブル
なし。



---------------------------------------------------------------------------------------------------------------------------

予防接種事務デジタル化に係る
予診情報・予防接種記録管理／請求支払システムの
設計・開発及び運用・保守業務一式

ソフトウェアフレームワーク機能設計書

FW_S_CLIENT_08
AJAXクライアント



第1.1版
 
改版履歴
版数	制改訂
日付	変更
箇所	変更理由・変更内容	変更者	承認者
1.0	2025/03/24	-	初版発行	NTTD米山	NTTD 馬場
1.1	2025/05/02	-	詳細設計の反映	NTTD 甲斐	NTTD 馬場
					
					
					
 
目次
1.	機能概要	4
1.1.	概要	4
1.2.	API一覧	4
2.	機能実現方式	4
2.1.	利用ライブラリ	4
2.2.	コンポーネント構成	4
2.3.	実現方式	4
2.3.1.	Cookieの扱い	5
2.3.2.	リクエストタイムアウト	5
2.3.3.	通信エラーの処理	6
2.3.4.	リトライ	6
3.	機能詳細	7
3.1.	API仕様	7
3.1.1.	HttpClientクラス	7
3.1.2.	ApiRequestConfigインタフェース	23
3.1.3.	ApiResponseインタフェース	23
3.1.4.	ApiResponseResultインタフェース	23
3.1.5.	HttpClientErrorHandlerインタフェース	24
3.1.6.	DefaultHttpClientErrorHandlerクラス	25
3.1.7.	ユーティリティ関数	28
3.2.	プロパティ／環境変数	31
4.	利用例	31
4.1.	Getリクエスト	31
4.2.	Postリクエスト	33
4.3.	Putリクエスト	34
4.4.	Deleteリクエスト	34
4.5.	ファイルアップロード	35
4.6.	ファイルダウンロード	37
4.7.	エラーハンドリング	38
5.	その他	40
5.1.	システム基盤とのインタフェース	40
5.1.1.	サーバのURLドメインの配慮	40
5.2.	利用するDBテーブル	40


 
1.	機能概要
1.1.	概要
Ajax通信のための以下の汎用的な機能を提供する。
・GET、POSTメソッド等での通信
・ファイルアップロード、ダウンロードの通信

1.2.	API一覧
本機能が提供するAPIの一覧を以下に示す。

API名（論理）	API名（物理）	説明
AJAXクライントのコンストラクタ	HttpClient(config: ApiRequestConfig<null>, errorHandler: HttpClientErrorHandler)
引数のconfig を使用して HTTP クライアントのインスタンスを作成する。
configには以下を設定する
・ベースURL,
・タイムアウト値
・ヘッダ
errorHandlerが未指定の場合は、デフォルトのHttpClientErrorHandlerに基づく通信エラー処理実施
GETリクエスト送信	HttpClient.get<T> (url: string, params?:object, config?: ApiRequestConfig<null>): Promise<ApiResponse<T>>メソッド	GETリクエストを実行し、データを取得する。
POSTリクエスト送信	HttpClient.post<D, T> (url: string, data?: D, config?: ApiRequestConfig <D>): Promise<ApiResponse<T>>メソッド
POSTリクエストを実行し、データを送信する。
PUTリクエスト送信	HttpClient.put<D, T> (url: string, data?: D, config?: ApiRequestConfig<D>): Promise<ApiResponse<T>>メソッド
PUTリクエストを実行し、指定リソースを更新する。
DELETEリクエスト送信	HttpClient.delete<D,T> (url: string, data?: D, config?: ApiRequestConfig<D>): Promise<ApiResponse<T>>メソッド
DELETEリクエストを実行し、指定リソースを削除する。
ファイルアップロード（単一）	HttpClient.uploadFile<T> (url: string, file: Record<string, File>, additionalData?: Record<string, any>, onProgress?: (progress: UploadProgress) => void, config?: ApiRequestConfig<T>): Promise<ApiResponse<T>>メソッド
単一ファイルをアップロードする。
ファイルアップロード（複数）	HttpClient.uploadFiles<T> (url: string, files: Record<string, File []>, additionalData?: Record<string, any>, onProgress?: (progress: UploadProgress) => void, config?: ApiRequestConfig<T>): Promise<ApiResponse<T>>メソッド
複数ファイルを一度にアップロードする。
ファイルダウンロード	HttpClient.downloadFile<D> (url: string, method: 'GET' | 'POST' = 'GET', data?: D, onProgress?: (progress: DownloadProgress) => void,
Config?: ApiRequestConfig<D> ): Promise<Blob>メソッド
指定されたURLからファイルをダウンロードする。
通信エラー処理	HttpClientErrorHandler.handleError(error: unknown)
各通信のエラーの内容を判定し業務APの要件に応じた種別のエラーを返すインタフェース。
通常はデフォルト実装のDefaultClientErrorHanlderを利用する。
  
2.	機能実現方式
2.1.	利用ライブラリ
本機能が利用する主なライブラリを以下に示す。

ライブラリ名	説明
Axios	Promise注1ベースのAPIで実装するHttpの非同期通信クライアント
AxiosRetry	HTTPリクエスト時に一時的なエラーや通信障害に対して、自動的に再試行を行うAxios拡張ライブラリ
注1：　非同期処理の結果をコールバックで実装する組み込みのオブジェクトhttps://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Global_Objects/Promise

2.2.	コンポーネント構成
コンポーネントの相関図を以下に示す。
 

2.3.	実現方式
Axiosを利用して実装する。AxiosではHTTP通信の結果をPromise型で返却する。

通常の通信では、HTTPのContent-Typeヘッダを「application/json」に設定し、指定されたデータをJSON形式で送信する。
サーバからの応答がエラー応答の場合、ステータスコードやレスポンス情報等から判断しシステム共通のエラーハンドリング処理を実施する場合には、axiosのInterceptors機能を使って実装する。

ファイルアップロード時は、Content-Typeに「multipart/form-data」を設定し、指定されたFileオブジェクトデータを送信する。

ファイルダウンロード時は、以下の流れで実装する。
①	Axiosを利用して、指定されたURLからバイナリデータを取得する。
②	①で取得したバイナリデータをBlobオブジェクトに変換し、ブラウザがダウンロードするためのリンクを生成する。
③	②で生成したリンクをクリックするイベントをトリガーし、ブラウザのダウンロードダイアログを表示させる。

2.3.1.	Cookieの扱い
ソフトウェアフレームワーク機能「FW_J_ONLINE_07_IDPW認証」では、SPAの方式ではあるが、二重ログイン防止の要件からサーバでセッション情報を管理する。クライアントのセッションIDの管理にはCookieを利用する。
このため、本機能は、サーバ処理要求時に、ログイン済である場合にセッションIDをサーバへ送信する必要があり、Cookieを送信する機能が必要である。
Axiosの場合、Cookieをサーバに送信するためのオプションとして、withCredentialsオプションをtrueに設定する。
本システムでは通常Cookieの送信が必要なので、デフォルト値は、trueとする。
2.3.2.	リクエストタイムアウト
Axiosによる通信のリクエストタイムアウト（ms）のデフォルト値は以下とする。

デフォルトのタイムアウト値：10000ms

個別でタイムアウト設定をしたい場合はインスタンス生成、または各メソッドの引数で設定可能にする。


 
2.3.3.	通信エラーの処理
通信エラー時の処理は、HttpClientErrorHandlerインタフェースを実装する。本機能ではデフォルト実装であるDefaultHttpClientErrorHandlerを使用することで、HTTP通信でのエラーは、サーバからエラーレスポンスの種類に応じて、業務エラーまたはシステムエラーに。エラーレスポンスに応じた挙動については「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。
そのため、通常、業務開発者は、独自でハンドリングをする必要はない。基本的にはデフォルトのエラーハンドラと集約例外ハンドリングの機能を使用して、上位の呼び出し元へ例外をスローして、「FW_S_CLIENT_09_集約例外ハンドリング（クライアント）」の機能を用いて、ハンドリングする。
なお、業務独自でエラーハンドリングをしたい場合は、DefaultHttpClientErrorHandler を継承するか、HttpClientErrorHandlerインタフェースを直接実装して、カスタムエラーハンドラを定義し、HttpClientのコンストラクタで渡すことで適用することができる。
2.3.4.	リトライ
本機能では、ネットワークエラーの場合、または特定のHTTPメソッドのHTTPステータスが返却された場合にリトライを行う。
Axios Retryを利用して実装し、リトライ対象のHTTPステータスコード、リトライ回数、およびリトライ間隔を指定しておく。
本機能は、画面APを前提としたクライアント-サーバ間の通信を想定してるため、リトライ設定は以下の通りとする。
Axios Retryの設定方法についてはAxio Retryのドキュメントを参照のこと

・	リトライ対象のエラー原因
Axios Retryのデフォルト判定条件（isNetworkOrIdempotentRequestError関数による判定）に基づき、以下のいずれかのエラーに該当する場合とする。

	ネットワークエラー（isNetworkError関数による判定）
	ただし、リクエストタイムアウト（AxiosErrorのコードが、ECONNABORTED）、ユーザキャンセル（ERR_CANCELED）はリトライ対象外
	冪等なリクエストのサーバエラー（isIdempotentRequestError関数による判定）
	HTTPメソッドがGET、HEAD、OPTIONS、PUT、DELETEのリクエストで、HTTPステータスコードが429、5xxの場合

・	リトライ回数
Axios Retryのデフォルト値と同様の値とする。
	3回

・	リトライ間隔
Axios Retryのエクスポネンシャルバック（exponentialDelay関数）によるリトライのデフォルト値とする。
	ジッター：リトライ間隔の0～20%までのランダム秒を加算（delayFactor=100）


3.	機能詳細
3.1.	API仕様
本機能が提供するAPIの仕様を以下に示す。

3.1.1.	HttpClientクラス
本機能が提供するHTTP通信を実施するAPI仕様について以下に示す。

メソッド名	メソッド概要	I/O	論理項目名	物理項目名	データ型
コンストラクタ	HttpClient	I	コンフィグ	config	ApiRequestConfig <null>
		I	エラーハンドラ	errorHandler注1	DefaultHttpClientErrorHandler
		I	リトライ回数	retries	number
		I	リトライ対象ステータスコード	retryHttpStatusCodes	number[]
		O	―	―	―
GETリクエスト	HttpClient. get	I	取得先のURL	url	string

		I	クエリパラメータ	params注1	Object
		I	APIリクエストコンフィグ	config注1	ApiRequestConfig<null>
		O	APIレスポンス	response	Promise<ApiResponse<T>>
POSTリクエスト	HttpClient. post	I	送信先のURL	url	String

		I	データ	data注1	D
		I	コンフィグ	config注1	ApiRequestConfig<D>
		O	APIレスポンス	response	Promise<ApiResponse<T>>
PUTリクエスト	HttpClient. put	I	更新対象のURL	url	String

		I	データ	data注1	D

		I	コンフィグ	config注1	ApiRequestConfig<D>

		O	APIレスポンス	response	Promise<ApiResponse<T>>

DELETEリクエスト	HttpClient. delete	I	削除対象のURL	url	String

		I	データ	data注1	D

		I	コンフィグ	config注1	ApiRequestConfig<D>

		O	APIレスポンス	response	Promise<ApiResponse<T>>

ファイルアップロード（単一）	HttpClient. uploadFile	I	アップロード先のURL	url	string
		I	アップロードするファイル	fileRecord	Record<string,File>
		I	フォームデータに追加するオプションデータ	additionalData注1	Record<string, any>
		I	アップロード進捗を通知するコールバック	onProgress注1	(progress: UploadProgress) => void
		I	コンフィグ	config注1	ApiRequestConfig<FormData>
		O	APIレスポンス	response	Promise<ApiResponse<T>>
ファイルアップロード（複数）	HttpClient. uploadFiles	I	アップロード先のURL	url	string
		I	アップロードするファイルの配列	fileRecord	Record<string,File[]>
		I	フォームデータに追加するオプションデータ	additionalData注1	Record<string, any>
		I	アップロード進捗を通知するコールバック	onProgress注1	(progress: UploadProgress) => void
		I	コンフィグ	config注1	ApiRequestConfig<FormData>
		O	APIレスポンス	response	Promise<ApiResponse<T>>
ファイルダウンロード
	HttpClient. downloadFile	I	ダウンロード先のURL	url	string
		I	HTTPメソッド	method	String

		I	データ	data注1	D

		I	ダウンロード進捗を通知するコールバック	onProgress注1	(progress: DownloadProgress) => void
		I	コンフィグ	config注1	ApiRequestConfig<D>

		O	ダウンロードしたファイルのBlob	blob	 Promise<Blob>
注1：省略可能

 
①	HttpClientコンストラクタ
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
コンフィグ	config	ApiRequestConfig	各HTTP通信で使用する既定のHttpクライアントのオプションを設定する。
エラーハンドル	errorHandler	HttpClientErrorHandler	省略可能。設定はHttpClientErrorHandlerインタフェースを実装したクラスを設定することで個別にエラー処理の設定が行える。
設定省略した場合、デフォルト実装であるDefaultHttpClientErrorHandlerクラスが使用される。
リトライ回数	retries	number	省略可能。
通信時のリトライ回数を設定する。デフォルトは3回。
リトライ対象ステータスコード	retryHttpStatusCodes	number[]	省略可能。
リトライ対象のステータスコードを設定する。デフォルトはなし。

(ウ)	出力情報
なし。

(エ)	処理ロジック
i.	HttpClientのインスタンス生成
引数で受け取ったオブジェクトをインスタンス変数に設定する。
ii.	リトライ設定
通信時のリトライ設定を行う。
iii.	その他設定
通信の前後で行うインターセプタ処理として通信エラー処理の設定を行う。

(オ)	例外
なし。
 

②	HttpClient.getメソッド
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
 取得先のURL	url	string	
クエリパラメータ	params	object	省略可能
コンフィグ	config	ApiRequestConfig	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
APIレスポンスのデータ	-	Promise<ApiResponse<T>>	

(エ)	処理ロジック
i.	getリクエスト送信
Axiosのgetメソッドを使用して、指定されたURLからデータを取得する。クエリパラメータがある場合は、それも一緒に送信する。

ii.	結果返却
APIレスポンスのデータとステータスコードをオブジェクトにして返す。

(オ)	例外
getリクエスト送信時にエラーが発生した場合、HttpClientErrorHandlerインタフェースhandleErrorメソッドの実装に基づくカスタムエラーをスローする。
デフォルト実装のDefaultHttpClientErrorHandlerによりスローされるエラーについては、「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。

 

③	HttpClient.postメソッド
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
 送信先のURL	url	string	
 データ	data	any	省略可能
コンフィグ	config	ApiRequestConfig	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
APIレスポンスのデータ	-	Promise<ApiResponse<T>>	

(エ)	処理ロジック
i.	postリクエスト送信
Axiosのpostメソッドを使用して、指定されたURLにデータを送信し、リソースを登録する。登録するデータをリクエストボディに含めて送信する。

ii.	結果返却
APIレスポンスのデータとステータスコードをオブジェクトにして返す。

(オ)	例外
postリクエスト送信時にエラーが発生した場合、HttpClientErrorHandlerインタフェースhandleErrorメソッドの実装に基づくカスタムエラーをスローする。
デフォルト実装のDefaultHttpClientErrorHandlerによりスローされるエラーについては、「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。

 

④	HttpClient.putメソッド
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
  更新対象のURL	url	string	
  データ	data	any	省略可能
コンフィグ	config	ApiRequestConfig	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
APIレスポンスのデータ	-	Promise<ApiResponse<T>>	

(エ)	処理ロジック
i.	putリクエスト送信
Axiosのput()メソッドを使用して、指定されたURLにデータを送信し、リソースを更新する。更新するデータをリクエストボディに含めて送信する。

ii.	結果返却
APIレスポンスのデータとステータスコードをオブジェクトにして返す。

(オ)	例外
Putリクエスト送信時にエラーが発生した場合、HttpClientErrorHandlerインタフェースhandleErrorメソッドの実装に基づくカスタムエラーをスローする。
デフォルト実装のDefaultHttpClientErrorHandlerによりスローされるエラーについては、「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。


⑤	HttpClient.deleteメソッド
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
  削除対象のURL	url	string	
  データ	data	any	省略可能
コンフィグ	config	ApiRequestConfig	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
APIレスポンスのデータ	-	Promise<ApiResponse<T>>	

(エ)	処理ロジック
i.	deleteリクエスト送信
Axiosのdeleteメソッドを使用して、指定されたURLへ送信し、リソースを削除する。必要に応じて、追加のデータをリクエストボディに含める。

ii.	結果返却
APIレスポンスのデータとステータスコードをオブジェクトにして返す。

(オ)	例外
deleteメソッド送信時にエラーが発生した場合、HttpClientErrorHandlerインタフェースhandleErrorメソッドの実装に基づくカスタムエラーをスローする。
デフォルト実装のDefaultHttpClientErrorHandlerによりスローされるエラーについては、「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。



⑥	HttpClient.uploadFileメソッド
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
アップロード先のURL	url	string	
アップロードするファイル	file	Record<string, File>	
フォームデータに追加するオプションデータ	additionalData	Record<string, any>	省略可能
アップロード進捗を通知するコールバック	onProgress	(progress: UploadProgress) => void	省略可能
コンフィグ	config	ApiRequestConfig	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
APIレスポンスのデータ	-	Promise<ApiResponse<T>>	

(エ)	処理ロジック
i.	FormData追加
送信するファイルをFormDataに追加する。必要に応じて、追加のデータもFormDataに追加する。ファイルがNull、またはundefinedの場合、文字列化された送信されることを防止するため、追加の対象としない。
ii.	ファイルアップロード
Axiosのpost()メソッドを使用して、指定されたURLにFormDataをアップロードする。アップロード進捗を通知するコールバック関数を指定する。
iii.	結果返却
 APIレスポンスのデータとステータスコードをオブジェクトにして返す。

(オ)	例外
ファイルアップロード時にエラーが発生した場合、HttpClientErrorHandlerインタフェースhandleErrorメソッドの実装に基づくカスタムエラーをスローする。
デフォルト実装のDefaultHttpClientErrorHandlerによりスローされるエラーについては、「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。

 
⑦	HttpClient.uploadFilesメソッド
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
アップロード先のURL	url	string	
アップロードするファイルの配列	files	Record<string, File[]>	
フォームデータに追加するオプションデータ	additionalData	Record<string, any>	省略可能
アップロード進捗を通知するコールバック	onProgress	(progress: UploadProgress) => void	省略可能
コンフィグ	config	ApiRequestConfig	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
APIレスポンスのデータ	-	Promise<ApiResponse<T>>	



(エ)	処理ロジック
i.	FormData追加
送信するファイルの配列をFormDataに追加する。必要に応じて、追加のデータもFormDataに追加する。ファイルがNull、またはundefinedの場合、文字列化された送信されることを防止するため、追加の対象としない。
ii.	ファイルアップロード
Axiosのpost()メソッドを使用して、指定されたURLにFormDataをアップロードする。アップロード進捗を通知するコールバック関数を指定する。
iii.	結果返却
 APIレスポンスのデータとステータスコードをオブジェクトにして返す。

(オ)	例外
ファイルアップロード時にエラーが発生した場合、HttpClientErrorHandlerインタフェースhandleErrorメソッドの実装に基づくカスタムエラーをスローする。
デフォルト実装のDefaultHttpClientErrorHandlerによりスローされるエラーについては、「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。
 

⑧	HttpClient.downloadFileメソッド
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
 ダウンロード先のURL	url	string	
HTTPメソッド	method	string	デフォルトはGET
データ	data	any	省略可能
POSTメソッド等でデータ送信する際に使用する
ダウンロード進捗を通知するコールバック	onProgress	 (progress: DownloadProgress) => void	省略可能
コンフィグ	config	ApiRequestConfig	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
ダウンロードしたファイルのBlob	blob	Promise<Blob>	

(エ)	処理ロジック
i.	getまたはpostリクエストによるダウンロード処理要求
引数のHTTPメソッドに応じて、Axiosのget関数、またはpost関数を使用し、指定されたURLからデータをダウンロードする。

ii.	ダウンロード進捗の計算とコールバックの設定
Content-Lengthヘッダを利用してダウンロード進捗を計算し、ダウンロード進捗を通知するコールバック関数を指定する。また、レスポンスタイプを'blob'に設定する。

iii.	Httpレスポンスヘッダー取得
Content-Dispositionヘッダのfilenameからファイル名を抽出し、UTF-8でエンコードされたファイル名をデコードする。ファイル名が取得できない場合、デフォルトのファイル名を設定する。

iv.	結果返却
ダウンロードしたBlobデータからダウンロードリンクを作成し、クリックイベントを発生させる。ファイル名が指定されている場合は、ダウンロードリンクにファイル名を設定する。

(オ)	例外
ファイルダウンロード時にエラーが発生した場合、HttpClientErrorHandlerインタフェースhandleErrorメソッドの実装に基づくカスタムエラーをスローする。
デフォルト実装のDefaultHttpClientErrorHandlerによりスローされるエラーについては、「3.1.5DefaultHttpClientErrorHandlerクラス」を参照のこと。

 
3.1.2.	ApiRequestConfigインタフェース
HttpClientのAPIが共通して利用するリクエスト設定を定義する。
ApiRequestConfigは、AxiosのAxiosRequestConfigインタフェースの別名であり、同じものであるため、設定可能な項目は、Axiosのドキュメントを参照すること。

論理項目名	物理項目名	データ型	Null可否	備考
APIリクエスト設定型	ApiRequestConfig<T>	―	―	AxiosRequestConfigを設定し、Axiosのリクエスト設定を表すタイプ
3.1.3.	ApiResponseインタフェース
HttpClientのAPIが共通して利用するレスポンスデータのインタフェースを定義する。
ApiResponseインタフェースのプロパティを以下に示す。

論理項目名	物理項目名	データ型	Null可否	備考
データ	data	T	―	AxiosResponseのデータ
ステータスコード	status	number	―	AxiosResponseのステータスコード
メッセージ	message	string	―	メッセージ。省略可能

3.1.4.	ApiResponseResultインタフェース
サーバからのエラーレスポンスデータのインタフェースを定義する。
ApiResponseResultインタフェースのプロパティを以下に示す。

論理項目名	物理項目名	データ型	Null可否	備考
エラーコード	code	string	―	
エラーメッセージ	message	string	―	
エラーメッセージ詳細	details	string[]	―	省略可能


3.1.5.	HttpClientErrorHandlerインタフェース
通信エラー時のエラー処理を実装するためのインタフェースである。
HttpClientErrorHandler インタフェースのメソッドを以下に示す。

論理項目名	物理項目名	データ型	Null可否	備考
ハンドルエラー関数	handleError (error: unknown)	Error	―	HttpClientのエラーを制御する関数

 
3.1.6.	DefaultHttpClientErrorHandlerクラス
HttpClientErrorHandlerのデフォルト実装である。通常は、この実装を利用すればよい。handlerErrorメソッドの実装内容は以下の通りである。
サーバ通信エラーの種別によって、「FW_S_CLIENT_09 集約例外ハンドリング（クライアント）」機能が定義する各種カスタムエラーをスローする。

・	エラーが、Axiosによるサーバ通信エラー（AxiosError）の場合
	エラーレスポンスがない場合（ネットワークエラー）は、その他の警告エラー（OtherError）をスロー。
	スローするエラーの詳細は以下のとおりである。
プロパティ名	設定内容
code	w.fwsc08.8001
message	サービス呼び出し時にエラーが発生しました。しばらく経ってから実行してください。
details	undefined
cause	（エラー情報）

	エラーレスポンスが存在する場合、HTTPステータスコードに基づくエラーをスロー。
	401の場合、認証エラー（AuthenticationError）をスロー。
	サーバ側で、認証処理に失敗した場合に該当する。
	業務エラー（BusinessError）を継承する特殊なエラーである。
	スローするエラーの詳細は以下のとおりである。
プロパティ名	設定内容
code	e.fwsc08.9002
message	認証に失敗しました。再度ログインしてください。
details	なし
cause	なし


	403の場合、認可エラー（AuthorizationError）をスロー。
	サーバ側で、APIの認可に失敗した場合（権限のないユーザがURIを直接指定して処理実行した場合など）に該当する。
	その他の警告エラー（OtherError）を継承する特殊なエラーである。
	スローするエラーの詳細は以下のとおりである。
プロパティ名	設定内容
code	w.fwsc08.8002
message	認可に失敗しました。再度ログインしてください。
details	なし
cause	なし

	レスポンスデータの解析に失敗した場合、システムエラー（SystemError）をスロー。
	警告ログを出力する。
	スローするエラーの詳細は以下のとおりである。
プロパティ名	設定内容
code	e.fwsc08.9002
message	サーバからエラーが返却されましたがレスポンスの解析に失敗しました。
details	undefined
cause	（エラー情報）

	その他の400番台の場合、業務エラー（BusinessError）をスロー。
	サーバ側で、入力エラー、業務エラーが発生した場合に該当する。
	サーバからのエラーレスポンスに含まれるエラーメッセージが格納される。

	スローするエラーの詳細は以下のとおりである。
プロパティ名	設定内容
code	（レスポンスデータのコード）
message	（レスポンスデータのメッセージ）
details	（レスポンスデータの詳細）
cause	なし

	500番台の場合、システムエラー（SystemError）をスロー。
	サーバ側でシステムエラーが発生した場合に該当する。
	サーバからのエラーレスポンスに含まれるエラーメッセージが格納される。
	スローするエラーの詳細は以下のとおりである。
プロパティ名	設定内容
code	（レスポンスデータのコード）
message	（レスポンスデータのメッセージ）
details	（レスポンスデータの詳細）
cause	（エラー情報）

・	エラーが、Axiosによるサーバ通信エラー（AxiosError）でない場合
	想定外のエラーのため、システムエラー（SystemError）をスローする。
	スローするエラーの詳細は以下のとおりである。
プロパティ名	設定内容
code	e.fwsc08.9001
message	想定外のエラーが発生しました。
details	undefined
cause	（エラー情報）

 
3.1.7.	ユーティリティ関数
共通で利用するクラス外のユーティリティ関数のAPI仕様を以下に示す。

関数名	概要	I/O	論理項目名	物理項目名	データ型
isApiResponseResult	ApiResponseResultインタフェース型と一致するかチェックする。	I	レスポンスデータ	value	unknown
		O	判定結果	result	bool
createAxiosConfig	Axioの通信設定を作成する	I	コンフィグ	Config	ApiRequestConfig<null>
		O	コンフィグ	Config	AxiosRequestConfig


①	isApiResponseResult関数
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
レスポンスデータ	value	unknown	サーバからのレスポンスデータ

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
判定結果	value	unknown	レスポンスデータがApiResponseResultのインタフェースと一致する場合、trueを返却し、不一致の場合falseを返却する。

 

(エ)	処理ロジック
i.	引数のレスポンスデータの判定
以下いずれかの場合、ApiResponseResultのインタフェースと不一致とみなし、
falseを返す。以下いずれに該当しない場合、trueを返す。
	valueがオブジェクトでない、またはnull
	value内のcodeプロパティが文字列でない
	value内のmessageプロパティが文字列でない
	value内のdetailsプロパティが配列でない
	value内のdetailsプロパティのすべての要素が文字列でない

(オ)	例外
なし。
 

②	createAxiosConfig関数
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
コンフィグ	config	ApiRequestConfig	

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
コンフィグ	config	AxiosReqeustConfig	

(エ)	処理ロジック
ii.	Axiosのプロパティ設定
HttpClientのインスタンス時、Axios利用で設定する情報を設定する。
	baseURL
入力情報となるコンフィグのbaseURLを設定する。
	withCredentials
trueを設定する。
	timeout
入力情報となるコンフィグのtimeoutプロパティの値を設定する。
timeoutプロパティの値がなければデフォルトの10000を設定する。
	headers
HTTPリクエストヘッダのContent-Typeにapplication/jsonを設定する。
また、入力情報となるコンフィグにHTTPヘッダの追加設定がある場合、設定する。

(オ)	例外
なし。
 

3.2.	プロパティ／環境変数
なし。
4.	利用例
本機能の利用例を以下に示す。

4.1.	Getリクエスト
```
<script setup lang="ts">
import { HttpClient } from 'yysk-client-fw' // (1)

// APIレスポンスの型定義
interface User {
  id: number
  name: string
  email: string
}

// GETメソッドの利用例
async function fetchUsers() {
  const httpClient = new HttpClient({
    baseURL: 'https://api.example.com',
  }) //(2)
    const response = await httpClient.get<User[]>('/users', { active: true }) //(3)

    console.log('Users:', response.data) //(4)
    console.log('Status:', response.status)
}
</script>
```
(1)	ライブラリのインポート
AJAXクライアント機能を使用するために必要なライブラリをインポート。

(2)	HttpClient のインスタンス作成:
baseURL を指定して HttpClient を初期化する。

(3)	get メソッドの呼び出し:  
`/users` エンドポイントに対して GET リクエストを送信する。クエリパラメータとして `{ active: true }` を指定する。

(4)	レスポンスの処理:  
`response.data` に取得したデータが格納される。`response.status` には HTTP ステータスコードが格納される。

4.2.	Postリクエスト
```
<script setup lang="ts">
import { HttpClient } from 'yysk-client-fw' // (1)

interface Post {
  id: number
  title: string
  content: string
}
// POSTメソッドの利用例
async function createPost() {
  const httpClient = new HttpClient({   //(2)
    baseURL: 'https://api.example.com',
  }) 

  const newPost = {
    title: 'New Post',
    content: 'This is a new post.',
  }

  const response = await httpClient.post<Post, Post>('/posts', newPost) //(3)
  console.log('Created Post:', response.data)
}
</script>
```
(1)	ライブラリのインポート
AJAXクライアント機能を使用するために必要なライブラリをインポート。

(2)	HttpClient のインスタンス作成:
baseURL を指定して HttpClient を初期化する。

(3)	post メソッドの呼び出し:  
`/posts` エンドポイントに対して POST リクエストを送信する。リクエストボディとして新しい投稿データを送信する。

(4)	レスポンスの処理:  
`response.data` に作成された投稿データが格納される。`response.status` には HTTP ステータスコードが格納される。

4.3.	Putリクエスト
Postリクエストと同様のため、省略する。
4.4.	Deleteリクエスト
Postリクエストと同様のため、省略する。

4.5.	ファイルアップロード
```
<script setup lang="ts">
import { HttpClient } from 'yysk-client-fw' // (1)

// ファイルアップロードの利用例
async function uploadFileExample() {
  const httpClient = new HttpClient({
    baseURL: 'https://api.example.com',
  }) //(2)

  const file = new File(['file content'], 'example.txt', { type: 'text/plain' })
  const fileRecord = { fileKey: file }

  const response = await httpClient.uploadFile( //(3)
    '/files/upload',
    fileRecord,
    'POST',
    (progress) => {
      console.log('Upload Progress:', progress)( //(4)
    }, 
  )
  
console.log('Uploaded File Response:', response.data) //(5)
}
</script>

```
(1)	ライブラリのインポート
AJAXクライアント機能を使用するために必要なライブラリをインポート。

(2)	HttpClient のインスタンス作成:
baseURL を指定して HttpClient を初期化する。

(3)	uploadFile メソッドの呼び出し:  
`/files/upload` エンドポイントに対して GETもしくはPOST リクエストを送信し、単一ファイルをアップロードする。

(4)	進捗の通知:  
`onProgress` コールバックを使用して、アップロードの進捗状況を通知される。

(5)	レスポンスの処理:  
`response.data` にアップロード結果が格納される。
 
4.6.	ファイルダウンロード
```
<script setup lang="ts">
import { HttpClient } from 'yysk-client-fw' // (1)

// ファイルダウンロードの利用例
async function downloadFileExample() {
  const httpClient = new HttpClient({ //(2)
    baseURL: 'https://api.example.com',
  }) 

  const blob = await httpClient.downloadFile('/files/example', 'GET', undefined, (progress) => {
    console.log('Download Progress:', progress)
  }) //(3)
  
console.log('Downloaded Blob:', blob) //(4)(5)
}
</script>
```
(1)	ライブラリのインポート
AJAXクライアント機能を使用するために必要なライブラリをインポート。

(2)	HttpClient のインスタンス作成:
baseURL を指定して HttpClient を初期化する。

(3)	downloadFile メソッドの呼び出し:  
`/files/example` エンドポイントに対して GET リクエストを送信し、ファイルをダウンロードする。

(4)	進捗の通知:  
`onProgress` コールバックを使用して、ダウンロードの進捗状況を通知する。

(5)	レスポンスの処理:  
ダウンロードしたファイルは `Blob` として返す。
4.7.	エラーハンドリング
基本的にはデフォルトのエラーハンドラと集約例外ハンドリングの機能を使用して、上位の呼び出し元へ例外をスローして、「FW_S_CLIENT_09_集約例外ハンドリング（クライアント）」の機能を用いて、ハンドリングする。
なお、業務独自でエラーハンドリングをしたい場合は、DefaultHttpClientErrorHandler を継承するか、HttpClientErrorHandlerインタフェースを直接実装して、カスタムエラーハンドラを定義し、HttpClientのコンストラクタで渡すことで適用することができる。
Errorについては、BusinessError（業務エラー）を継承したカスタムエラークラスを作成するとよい。
本項の方法は業務個別でしか処理できない場合を除いて使用しない方針とする。
<エラーハンドラ定義>
```
// (1)
class CustomErrorHandler extends DefaultHttpClientErrorHandler {
    handleError(response: ApiResponse): Error {
      const errorData = response.data as ErrorResponse;
      
      // 特定のエラーコードに対して特別な処理
      switch (errorData.code) {
        case 'E001': // 在庫不足エラー
          // 在庫不足の場合、特別なエラーを返す（BusinessErrorを継承したカスタムエラーを定義）
          return new InventoryError(
            errorData.code, 
            '在庫が不足しています。', // (2)
            errorData.details
          );
        
        default:
          // それ以外は親クラスのデフォルト処理を使用
          return super.handleError(response);
      }
    }
}
```
(1)	カスタムエラーハンドラの定義
DefaultHttpClientErrorHandler を継承するか、HttpClientErrorHandlerインタフェースを直接実装して、カスタムエラーハンドラクラスを定義する。
(2)	エラーメッセージ
エラーメッセージを定義する。
※実際には、メッセージはメッセージ管理機能を利用して取得するとよい。取得方法は割愛する。

<HTTPリクエスト>
```
// (1)
const httpClient = new HttpClient(new CustomErrorHandler());

try {
  await httpClient.post('/order', orderData);
} catch (error) {
// (2)
  if (error instanceof InventoryError) {
    // 在庫不足固有の業務エラー時の処理を記載
  }
}

```
(1)	HttpClient初期化
カスタムエラーハンドラクラスをコンストラクタ引数として渡し、HttpClientを初期化する。
(2)	エラーハンドリング
HTTP通信でのエラーをキャッチして、処理する。
 
5.	その他
5.1.	システム基盤とのインタフェース
5.1.1.	サーバのURLドメインの配慮
本システムでは、SPAによる実現のため、静的コンテンツを配信するWebサーバと、サーバAPIを処理するAPサーバに分かれる。
ソフトウェアフレームワーク機能「FW_J_ONLINE_07_IDPW認証」については、セッションをCookieで管理するため、ログイン時、サーバAPIからSet-Cookieのレスポンスヘッダーで送信されたCookieをブラウザに保存し、以降の処理ではCookieを送信できる通信できるようにするために、WebサーバとAPサーバの間で、Cookieをやりとりできなければならない。
デフォルトのCookieは設定されたオリジンと同一でないと送信されないがサーバAP側で、発行するCookieのDomain属性に親ドメインを指定することで、その親ドメイン配下であれば常にCookieを送信できるようになる。
このため、各サーバのドメインが親子関係になるように、システム基盤にURL設計してもらう必要がある。

5.2.	利用するDBテーブル
なし。

---------------------------------------------------------------------------------------------------------------------------

予防接種事務デジタル化に係る
予診情報・予防接種記録管理／請求支払システムの
設計・開発及び運用・保守業務一式

ソフトウェアフレームワーク機能設計書

FW_S_CLIENT_11
メッセージ管理

第1.1版
 
改版履歴
版数	制改訂
日付	変更
箇所	変更理由・変更内容	変更者	承認者
1.0	2025/03/24	-	初版発行	NTTD米山	NTTD 馬場
1.1	2025/05/02	-	詳細設計の反映	NTTD米山	NTTD 馬場
					
					
					
 
目次
1.	機能概要	1
1.1.	概要	1
1.2.	API一覧	1
2.	機能実現方式	1
2.1.	利用ライブラリ	1
2.2.	コンポーネント構成	2
2.3.	実現方式	2
3.	機能詳細	3
3.1.	API仕様	3
3.1.1.	メッセージ管理	3
3.2.	プロパティ／環境変数	7
4.	利用例	8
4.1.	アプリケーション起動時のメッセージ定義読み込み	8
4.2.	コンポーネントでのメッセージ取得	9
5.	その他	10
5.1.	システム基盤とのインタフェース	10
5.2.	利用するDBテーブル	10

 
 
1.	機能概要
1.1.	概要
エラーメッセージ・通知メッセージ・確認ダイアログなど、アプリケーション内で使用されるメッセージを統一的に管理する。本機能では、定義されたメッセージIDを基に、アプリケーション内で使用するメッセージを統一的に取得できるようにする。

1.2.	API一覧
本機能が提供するAPIの一覧を以下に示す。

API名（論理）	API名（物理）	説明
メッセージ定義読込	Message.load<T extends { [key: string]: string }>(messages: T): voidメソッド
インスタンス生成時の初期化処理をする。
messagesに使用するメッセージの定義を設定する。
メッセージ取得	Message.getMessage(messageId: string, replaceElements?: string[]): stringメソッド
メッセージIDに紐づくメッセージ値を取得し返却する。

2.	機能実現方式
2.1.	利用ライブラリ
なし。

 
2.2.	コンポーネント構成
本機能を構成する主要コンポーネントとその関連を以下に示す。

	Messageパッケージコンポーネント図
 

・	コンポーネント一覧
パッケージ	コンポーネント	説明
messages	Message	メッセージ管理のためのクラス。シングルトンパターンで実装され、アプリケーション全体で1つのインスタンスを共有する。

2.3.	実現方式
2.4.	Messageクラス
シングルトンパターンで実装するため、コンストラクタはprivateアクセス修飾子を付与し定義することで外部からのインスタンス生成を制限する。


 
3.	機能詳細
3.1.	API仕様
本機能が提供するAPIの仕様を以下に示す。

3.1.1.	メッセージ管理
(1)	Messageクラス
本機能が提供するメソッドの一覧を以下に示す。
メソッド名	メソッド概要	I/O	論理項目名	物理項目名	データ型
メッセージ定義読込	Message.load	I	メッセージリスト	messages	T extends { [key: string]: string }
		O	―	―	―
メッセージ取得	Message. getMessage	I	メッセージID	messageId	string

		I	置換文字列配列	replaceElements	string[]
		O	メッセージ	―	string

 
①	Message.load<T extends { [key: string]: string }>(messages: T): voidメソッド
staticメソッドとして定義され、インスタンスを生成せずに呼び出すことができる。
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
メッセージリスト	messages	T	使用するメッセージの定義を設定する。

(ウ)	出力情報
なし。

(エ)	処理ロジック
i.	インスタンスの生成
引数で受け取ったオブジェクトを使用して自己インスタンス化を行い、staticなインスタンス変数instanceに保持する。

(オ)	例外
なし。
 
②	Message.getMessage(messageId: string, replaceElements?: string[]): stringメソッド
staticメソッドとして定義され、インスタンスを生成せずに呼び出すことができる。
(ア)	事前条件
なし。

(イ)	入力情報
論理項目名	物理項目名	データ型	備考
メッセージID	messageId	keyof T	コンストラクタで設定されたオブジェクトのキーのみを許容する型
置換文字列配列	replaceElements	string []	省略可能

(ウ)	出力情報
論理項目名	物理項目名	データ型	備考
メッセージ	-	string	置換処理済みのメッセージ文字列

(エ)	処理ロジック
i.	インスタンス生成確認
インスタンスが生成されていない場合、メッセージIDとプレースホルダーを返却する。
また、開発向けにwarnログでメッセージを出力する。

ii.	メッセージIDの存在確認
指定されたメッセージIDが存在しない場合、メッセージIDとプレースホルダーを返却する。
また、開発向けにwarnログでメッセージを出力する。

iii.	メッセージの取得
指定されたメッセージIDに紐づくメッセージを、インスタンス変数instanceのmessagesから取得する。

iv.	プレースホルダーの置換
置換文字列配列が指定されている場合、メッセージ内の{0}, {1}...のプレースホルダーを配列の要素で順次置換する。

v.	メッセージの返却
置換処理済みのメッセージを返却する。

(オ)	例外
なし。

3.2.	プロパティ／環境変数
なし。
 
4.	利用例
本機能の利用例を以下に示す。
4.1.	アプリケーション起動時のメッセージ定義読み込み
単一のインスタンスでメッセージを管理するため、メッセージ定義の読み込みは1か所のみで行う必要がある。読み込み箇所に関しては、メッセージ機能を使用する前に初期化を行うため、アプリケーション起動時の処理を記述するmain.tsに実装することを推奨する。複数の業務や機能に分けてメッセージ定義する場合は、それらを結合してからMessageクラスのstaticメソッドであるloadメソッドに渡す。
また、loadメソッドを複数回実行すると、メッセージ定義のリスト全体が最後に読み込んだメッセージ定義で上書きされることに注意が必要である。
	main.tsの実装例
```
import { createApp } from 'vue'
import App from './App.vue'
import { Message } from 'yysk-client-fw' // (1)
import { COMMON_MESSAGE_LIST } from './usecases/shared/messages/messageList'
import { VACCINATION_MESSAGE_LIST } from './usecases/vaccination/shared/messages/messageList'
import { BILLING_MESSAGE_LIST } from './usecases/billing/shared/messages/messageList'

// スプレッド構文を使用してメッセージ定義を結合
const MESSAGE_LIST = {
  ...COMMON_MESSAGE_LIST,
  ...VACCINATION_MESSAGE_LIST,
  ...BILLING_MESSAGE_LIST
}
Message.load(MESSAGE_LIST) // (2)

const app = createApp(App)
// app.use等のアプリケーションの設定を記載
app.mount('#app')
```
(1)	メッセージ機能を使用するために必要なライブラリをインポート。
(2)	結合したメッセージ定義を読み込む。
 
4.2.	コンポーネントでのメッセージ取得
MessageクラスのstaticメソッドであるgetMessageメソッドにより、メッセージIDを指定してメッセージを取得する。必要に応じてプレースホルダーの置換も可能である。
	getMessage()の実装例
```
<script setup lang="ts">
import { ref } from 'vue'
import { Message } from 'yysk-client-fw' // (1)

const message1 = ref('')
const message2 = ref('')

const checkType = () => {
  message1.value = Message.getMessage('i.A01.001') // (2)
  message2.value = Message.getMessage(' i.A01.002', ['置換文字']) // (3)
}
</script>
```
(1)	メッセージ機能を使用するために必要なライブラリをインポート。
(2)	パラメータ置換が不要なメッセージを取得。getMessage()の第1引数にメッセージIDを指定。
(3)	パラメータ置換が必要なメッセージを取得。getMessage()の第2引数に配列を指定し、メッセージ内の{0}, {1}...のプレースホルダーを配列の要素で順次置換する。
 
5.	その他
5.1.	システム基盤とのインタフェース
なし。

5.2.	利用するDBテーブル
なし。
