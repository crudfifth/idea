```
backend-root/
├─ Cargo.toml                           # Rust パッケージ定義
├─ Cargo.lock                           # ロックファイル（CI で使う）
├─ .env                                 # 環境変数（ローカル用）
├─ .env.example                         # 環境変数のサンプル
├─ sonar-project.properties             # SonarQube 設定（必要なら）
│
├─ migrations/                          # DBマイグレーション (sqlx, refinery, goose 等)
│  ├─ XXXXXXXXXX_create_users.sql
│  └─ ...
│
├─ src/                                 # サーバー本体
│  ├─ main.rs                           # エントリポイント（超薄く：bootstrap 呼び出すだけ）
│  │
│  ├─ app/                              # アプリ全体のフレーム・起動周り（Composition Root）
│  │  ├─ config.rs                      # 設定読み込み (env, configファイル, 型定義)
│  │  ├─ router.rs                      # ルートルーター定義（各 <domain> のルーターをマウント）
│  │  ├─ server.rs                      # Axum サーバー起動（Router + Layer の組立）
│  │  ├─ middleware.rs                  # アプリ共通ミドルウェア (logging, tracing, cors 等)
│  │  └─ bootstrap.rs                   # main.rs から呼ばれる起動処理 (run() 的なやつ)
│  │
│  ├─ shared/                           # 全ドメイン横断の「共通部品」層
│  │  ├─ error.rs                       # アプリ共通エラー型 (AppError, Result エイリアス 等)
│  │  ├─ types.rs                       # 共通型 (Id, Timestamp, Pagination 等)
│  │  ├─ util.rs                        # 汎用ユーティリティ（文字列, 日付, パス 等）
│  │  ├─ tracing.rs                     # ロギング/トレーシング初期化 (tracing-subscriber 等)
│  │  ├─ security.rs                    # パスワードハッシュ, JWT, 認可ヘルパ
│  │  ├─ db.rs                          # DBプール生成 (sqlx::PgPool など)・接続管理
│  │  └─ config_types.rs                # 設定の型定義 (AppConfig, DbConfig 等)
│  │
│  ├─ features/                         # 各 <domain> ごとの「機能モジュール」
│  │  └─ <domain>/                      # 例: user, project, auth, matching, entry, billing ...
│  │     ├─ domain/                     # ドメイン層（ビジネスの中心・不変ルール）
│  │     │  ├─ entities.rs              # エンティティ (User, Project, MatchingJob 等)
│  │     │  ├─ value_objects.rs         # 値オブジェクト (Email, UserName, Money 等)
│  │     │  ├─ repository.rs            # リポジトリトレイト (UserRepository など)
│  │     │  ├─ events.rs                # ドメインイベント (UserRegistered 等が必要なら)
│  │     │  └─ errors.rs                # ドメインエラー (DomainError, ValidationError 等)
│  │     │
│  │     ├─ application/                # アプリケーション層（ユースケース / サービス）
│  │     │  ├─ usecases/                # 1ユースケース単位のモジュール
│  │     │  │  ├─ create_xxx.rs         # 例: create_user, update_project 等
│  │     │  │  ├─ update_xxx.rs
│  │     │  │  └─ list_xxx.rs
│  │     │  ├─ dto.rs                   # 入出力DTO（API I/Oではなくユースケース I/O の型）
│  │     │  ├─ service.rs               # ユースケースをまとめるサービスクラス的なもの
│  │     │  └─ errors.rs                # アプリケーション層のエラー (UsecaseError 等)
│  │     │
│  │     ├─ infrastructure/             # インフラ層（実際のDB/外部APIとの接続）
│  │     │  ├─ repository_impl.rs       # repository.rs の実装 (DBアクセス)
│  │     │  ├─ models.rs                # DBテーブル対応の構造体 (sqlx::FromRow 等)
│  │     │  ├─ queries.sql              # SQL文 (sqlx 用 query ファイルなどを分けたい場合)
│  │     │  └─ external_client.rs       # 外部APIクライアント (HTTP, gRPC 等)
│  │     │
│  │     └─ interface/                  # インターフェース層（外界との境界）
│  │        └─ http/                    # HTTP API (Axum用)
│  │           ├─ handlers.rs           # ハンドラー (axum::extract で入力, JSONで出力 等)
│  │           ├─ routes.rs             # この <domain> 専用の Router (RouteRecord 的なもの)
│  │           ├─ request.rs            # HTTPリクエスト用型 (Axum Extractor対応 DTO)
│  │           ├─ response.rs           # HTTPレスポンス用型 (API契約としてのDTO)
│  │           └─ extractors.rs         # カスタムExtractor (AuthUser, Tenant など)
│  │
│  └─ bin/                              # 複数バイナリがある場合に格納 (batch, worker 等)
│     └─ worker.rs                      # ジョブキュー用ワーカー
│
├─ tests/                               # Rust 標準の integration tests
│  ├─ health_check.rs                   # /health の疎通確認
│  ├─ <domain>_api_tests.rs             # 各ドメインのAPI統合テスト
│  └─ ...
│
├─ benches/                             # ベンチマーク (criterion 等)
│
├─ scripts/                             # 開発支援スクリプト
│  ├─ dev.sh                            # 開発サーバ起動
│  ├─ migrate.sh                        # DBマイグレーション実行
│  └─ seed.sh                           # 開発用シードデータ投入
│
└─ __config__/                          # ツール・CI 向け設定（コードからは基本 import しない）
   ├─ sonar/                            # sonar-project.properties を分割したい場合など
   ├─ ci/                               # GitHub Actions / GitLab CI 用テンプレ YAML
   ├─ docker/                           # Dockerfile, docker-compose 断片
   ├─ fmt/                              # rustfmt.toml, clippy 設定を分けたいなら
   └─ env/                              # 環境変数テンプレや schema 定義 等
```
