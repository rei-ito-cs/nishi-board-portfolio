# NISHI Board — システム全体構成図

既存の構成図をもとに、実装ファイルへの参照を省いた紹介用の図です。

```mermaid
flowchart TB
  subgraph LAN["信頼する店内ローカルネットワーク"]
    B["店内PC・タブレットのブラウザ"]
    FE["HTML・CSS・JavaScript<br/>ビルド不要の画面"]
    subgraph PC["店舗PCのNode.js・Expressサーバー"]
      S["Express起動・静的ファイル配信"]
      MW["Origin照合・JSON解析<br/>メモリ上のセッション"]
      AUTH["ログイン・セッション確認"]
      HEALTH["稼働確認"]
      GATE["セッション認証"]
      API["予約・卓・顧客・履歴・集計API"]
      BK["JSONエクスポート・インポート"]
      ERR["入力不正・内部エラーの応答"]
      AUTO["日次・起動時の自動バックアップ"]
      DB["SQLite・better-sqlite3"]
      GZ["店舗PC上のgzipバックアップ"]
    end
    FILE["手動で書き出す・読み込むJSONファイル"]
  end
  B --> FE
  S -->|"画面を配信"| FE
  FE -->|"HTTP・セッションCookie"| MW
  MW --> AUTH
  MW --> HEALTH
  MW --> GATE
  MW -.->|"解析などで失敗"| ERR
  GATE -->|"認証済み"| API
  GATE -->|"復元はAdmin限定"| BK
  API -->|"参照・更新"| DB
  BK -->|"参照・検証・復元"| DB
  BK -->|"書き出し"| FILE
  FILE -->|"読み込み"| BK
  S -->|"起動時に開始"| AUTO
  AUTO -->|"共通の書き出し処理"| BK
  AUTO -->|"gzip圧縮・保存"| GZ
```

## この図で伝えたいこと

店内のブラウザから、店舗PC1台のサーバーに接続する構成です。<br>
業務データはSQLiteに保存し、手動のJSON入出力と自動バックアップは共通の書き出し処理を使います。<br>
インターネット公開用の構成ではなく、紹介ページに実データや実装コードは含めていません。

[紹介ページへ戻る](../../README.md)
