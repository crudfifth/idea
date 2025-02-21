#### SWFWリポジトリ（ yysk-client-fw）のディレクトリ構成: 
```
yysk-client-fw/
├─ src/
│   ├─ validation/                           # 入力チェック機能
│   │   ├─ RequiredFieldValidator.ts         # 必須フィールドのバリデーション
│   │   ├─ EmailValidator.ts                 # メール形式のバリデーション
│   │   ├─ ValidationRules.ts                # その他のバリデーションルール
│   │   └─ __tests__/                        # テスト
│   │       ├─ RequiredFieldValidator.test.ts
│   │       ├─ EmailValidator.test.ts
│   │       └─ ValidationRules.test.ts
│   │       
│   ├─ exception/                            # 例外クラス
│   │   ├─ CustomError.ts                    # カスタムエラークラス
│   │   ├─ ValidationError.ts                # バリデーションエラー
│   │   └─ __tests__/                        # テスト
│   │       ├─ CustomError.test.ts
│   │       └─ ValidationError.test.ts
│   │       
│   ├─ exceptionhandler/                     # 例外ハンドリング機能
│   │   ├─ GlobalExceptionHandler.ts         # グローバル例外ハンドラー
│   │   ├─ ErrorHandler.ts                   # エラーハンドリングロジック
│   │   └─ __tests__/                        # テスト
│   │       ├─ GlobalExceptionHandler.test.ts
│   │       └─ ErrorHandler.test.ts
│   │       
│   ├─ messages/                             # メッセージ管理機能
│   │   ├─ MessageUtil.ts                    # メッセージ処理ユーティリティ
│   │   └─ __tests__/                        # テスト
│   │       └─ MessageUtil.test.ts
│   │       
│   └─ utils/                                # 汎用的なユーティリティ関数
│       ├─ DateUtil.ts                       # 日付処理ユーティリティ
│       ├─ StorageUtil.ts                    # ストレージ操作関数
│       └─ __tests__/                        # テスト
│           ├─ DateUtil.test.ts
│           └─ StorageUtil.test.ts
│
└─ README.md                                 # プロジェクト説明
```

#### 業務リポジトリ（yysk-client）のディレクトリ構成:
```plantext
yysk-client/                               # 業務リポジトリ
├── public/                    
│   ├── favicon.ico                        # ファビコン
│   ├── robots.txt                         # SEO クローラー用設定
│   ├── security.txt                       # セキュリティコンタクト情報（必要に応じて）
│   ├── images/                            # 画像ファイル
│   └── fonts/                             # フォント
│ 
├── src/
│   ├── components/                        # UI コンポーネント群
│   │   ├── icons/                         # SVG アイコンコンポーネント
│   │   │   └──  ~Icon.vue                 # 各アイコンコンポーネント
│   │   ├── layout/                        # レイアウト関連コンポーネント
│   │   │   ├── __tests__/                 # UIコンポーネントのテスト
│   │   │   │   └─ Layout.test.ts          # 各コンポーネントテストファイル
│   │   │   ├── Layout.vue                 # ボタンコンポーネント
│   │   │   └── Layout.stories.ts          # Storybookの定義
│   │   │
│   │   ├── link/                          # レイアウト関連コンポーネント
│   │   │   ├── __tests__/                 # UIコンポーネントのテスト
│   │   │   │   └─ Link.test.ts            # 各コンポーネントテストファイル
│   │   │   ├── Link.vue                   # ボタンコンポーネント
│   │   │   └── Link.stories.ts            # Storybookの定義
│   │   │
│   │   ├── checkbox/                      # レイアウト関連コンポーネント
│   │   │   ├── __tests__/                 # UIコンポーネントのテスト
│   │   │   │   └─ Checkbox.test.ts        # 各コンポーネントテストファイル
│   │   │   ├── Checkbox.vue               # ボタンコンポーネント
│   │   │   └── Checkbox.stories.ts        # Storybookの定義
│   │   │
│   │   ├── input/                         # フォーム関連コンポーネント
│   │   │   ├── __tests__/                 # UIコンポーネントのテスト
│   │   │   │   └─ Input.test.ts           # 各コンポーネントテストファイル
│   │   │   ├── Input.vue                  # ボタンコンポーネント
│   │   │   └── Input.stories.ts           # Storybookの定義
│   │   │
│   │   ├── radio/                         # レイアウト関連コンポーネント
│   │   │   ├── __tests__/                 # UIコンポーネントのテスト
│   │   │   │   └─ Radio.test.ts           # 各コンポーネントテストファイル
│   │   │   ├── Radio.vue                  # ボタンコンポーネント
│   │   │   └── Radio.stories.ts           # Storybookの定義
│   │   │ 
│   │   ├── form/                          # フォーム関連コンポーネント
│   │   │   ├── __tests__/                 # UIコンポーネントのテスト
│   │   │   │   └─ Form.test.ts            # 各コンポーネントテストファイル
│   │   │   ├── Form.vue                   # ボタンコンポーネント
│   │   │   └── Form.stories.ts            # Storybookの定義
│   │   │ 
│   │   ├── table/                         # テーブル関連コンポーネント
│   │   │   ├── __tests__/                 # UIコンポーネントのテスト
│   │   │   │   └─ Table.test.ts           # 各コンポーネントテストファイル
│   │   │   ├── Table.vue                  # ボタンコンポーネント
│   │   │   └── Table.stories.ts           # Storybookの定義
│   │   │ 
│   │   └── button/                        # SVG アイコンコンポーネント
│   │       ├── __tests__/                 # UIコンポーネントのテスト
│   │       │   └─ Button.test.ts          # 各コンポーネントテストファイル
│   │       ├── Button.vue                 # ボタンコンポーネント
│   │       └── Button.stories.ts          # Storybookの定義
│   │
│   ├── usecases/                          # 業務単位で分類された機能群（予診情報管理、請求）
│   │   ├── medical_record/                # 予診情報管理業務機能
│   │   │   └── ユースケース/
│   │   │       ├── views/                 # ビューコンポーネント
│   │   │       │   ├── __tests__/         # ビューテスト
│   │   │       │   ├── ~View.vue          # ビュー
│   │   │       │   └── ~View.stories.ts   # ストーリーブック用ビュー
│   │   │       │
│   │   │       └── services/              # 業務サービス
│   │   │           └── ~Service.ts        # 各業務サービスファイル
│   │   ├── invoicing/                     # 請求業務機能
│   │   │   └── ユースケース/
│   │   │       ├── views/                 # ビューコンポーネント
│   │   │       │   ├── __tests__/         # ビューテスト
│   │   │       │   ├── ~View.vue          # ビュー
│   │   │       │   └── ~View.stories.ts   # ストーリーブック用ビュー
│   │   │       │
│   │   │       └── services/              # 業務サービス
│   │   │           └── ~Service.ts        # 各業務サービスファイル
│   │   │
│   │   └── common/                        # 共通処理
│   │       ├── repositories/              # API リポジトリ層
│   │       │   └── ~Repository.ts/        # リポジトリファイル
│   │       ├── router/                    # ルーティング設定
│   │       ├── stores/                    # 状態管理
│   │       │   └── ~Store.ts              # 各ストアファイル
│   │       ├── messages/                  # 入力エラーメッセージ
│   │       ├── utils/                     # 汎用的なユーティリティ関数
│   │       └── styles/                    # スタイル設定
│   │
│   ├── mocks/                             # MSW用モック
│   ├── main.ts                            # エントリーポイント
│   └── App.vue                            # ルートコンポーネント
└── package.json                           # 依存関係管理
```
