

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
