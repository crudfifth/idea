```
src/
  app/                             # このアプリ全体を立ち上げるための“フレーム”層
    router/                        # 画面遷移の定義・ルーティング
      guards/                      # 認可チェックやログイン必須などのルートガード
    errors/                        # グローバルなエラーページ・エラーハンドリングUI
    layout/                        # アプリ全体のルートレイアウト（ヘッダ＋サイドバー等）
    providers/                     # DI, Queryクライアント, i18n などアプリ全体のプロバイダ

  components/                      # 業務ロジックを一切持たない汎用UIコンポーネント
    ui/                            # BaseButton, BaseInput, Icon など
    layout/                        # AppLayout用の細かいレイアウト部品など

  styles/                          # グローバルスタイル（reset, Tailwindエントリ 等）

  features/                        # 各 <domain> ごとの“業務UI＋業務ロジック”
    <domain>/                      # 例: user, project, matching, entry, sales, schedule...
      ui/                          # <domain> 専用のUI層（業務ロジックも絡む）
        pages/                     # 画面コンテナ（ルーティング直下で使うページ）
        components/                # <domain> 向け業務コンポーネント（一覧・フォーム等）
        composables/               # <domain> 画面専用のUIロジック (useXxx 系)
        styles/                    # <domain> 専用スタイル（スコープCSS, CSS Modules 等）

      logic/                       # <domain> 専用の“ロジックだけ”をまとめる層（UIは持たない）
        types/                     # DTOなど、<domain> 専用の型定義
        models/                    # <domain> モデル／エンティティ表現（DTO→Model変換＋振る舞い）
        services/                  # ユースケース（ビジネスロジック）例: executeLogin, updateProfile
        repositories/              # <domain> 向けAPIクライアント層（Axios 等でAPIを叩く）
        stores/                    # <domain> 専用の状態管理（Pinia ストアなど）
        validators/                # <domain> のフォーム／値チェックロジック
        messages/                  # <domain> 専用メッセージ（エラー文言・ラベル等）
        constants/                 # <domain> 専用定数（ステータス, ロール, フラグ値 等）
        errors/                    # <domain> 専用エラー型・例外（ドメインエラー・バリデーションエラー）

    ...                            # 他の <domain> も同じパターンで追加

  shared/                          # 全ドメイン横断で使い回す“共通ロジック＆共通スタイル”
    types/                         # 共通DTO・型定義
    entities/                      # 汎用エンティティ（どの <domain> からも参照されうる不変構造）
    models/                        # 複数 <domain> で使い回す汎用モデル
    services/                      # 特定 <domain> に属さない共通ユースケース
    repositories/                  # 共通APIクライアント層（HTTPクライアントのラッパ等）
    stores/                        # グローバルな状態（ログインユーザー, アプリ設定 等）
    composables/                   # 複数 feature で使うUIロジック (useToast, useConfirm 等)
    validators/                    # 共通バリデーション（メール形式, パスワードポリシー 等）
    constants/                     # 共通定数（APIベースURL, 汎用ステータス, 正規表現 等）
    messages/                      # 共通メッセージ（汎用エラー, 共通ラベル 等）
    errors/                        # 共通エラー型（HttpError, AppError など *ロジック側の型*）
    utils/                         # 汎用関数（日付フォーマット, 配列操作, 文字列ユーティリティ 等）
    http/                          # Axiosインスタンスや fetch ラッパなどHTTPクライアント共通層
    styles/                        # 共通スタイル（テーマ変数, デザイントークン, 汎用ユーティリティクラス）
    mocks/                         # MSW 等、開発用Mockの共通設定・ハンドラ

  mocks/                           # プロジェクト全体のモック起動エントリ（開発時bootstrap用 等）
                                   # 実装は shared/mocks や shared/http を利用する想定
```
