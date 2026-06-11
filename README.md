# 花粉エクスキュースジェネレーター — バックエンド

花粉症・限界突破エクスキュース（言い訳）ジェネレーターのバックエンドです。  
プロダクト全体の概要・セットアップ手順は [親リポジトリの README](https://github.com/hduehgw0/excuse-by-pollen) を参照してください。

## 技術スタック

| カテゴリ | 技術 |
|---|---|
| フレームワーク | Python / FastAPI |
| AI | Gemini 3 Flash（google-genai） |
| 花粉データ | Google Pollen API |
| データストア | Upstash Redis（upstash-redis） |
| デプロイ | Render.com |

## ディレクトリ構成

```
back/
├── src/
│   └── app/
│       ├── main.py           # FastAPI アプリ本体・CORS 設定
│       ├── api/
│       │   └── v3/           # 現行 API ルート
│       │       ├── generate.py    # 言い訳生成
│       │       ├── retry.py       # 言い訳の再生成（もっと盛る）
│       │       └── get_excuse.py  # シェア用言い訳取得
│       ├── services/v3/      # ビジネスロジック
│       ├── infra/v3/         # Redis クライアント
│       ├── schemas/          # リクエスト・レスポンスの型定義
│       └── prompts/          # Gemini へのプロンプトテンプレート
├── .env.sample               # 環境変数のサンプル
├── requirements.txt
└── Dockerfile
```

## API エンドポイント

| メソッド | パス | 説明 |
|---|---|---|
| `GET` | `/` | ヘルスチェック |
| `POST` | `/generate` | 言い訳を生成する |
| `POST` | `/retry` | 言い訳を追加指示で再生成する（もっと盛る） |
| `GET` | `/get-excuse/{excuse_id}` | ID に紐づく言い訳を取得する（シェア機能用） |

## 環境変数

`.env.sample` をコピーして `.env` を作成し、各値を設定してください。

```bash
cp .env.sample .env
```

| 変数名 | 内容 | 取得先 |
|---|---|---|
| `GEMINI_API_KEY` | Gemini API キー | Google AI Studio |
| `POLLEN_API_KEY` | Google Pollen API キー | Google Cloud Console |
| `UPSTASH_REDIS_REST_URL` | Upstash Redis の REST URL | Upstash コンソール |
| `UPSTASH_REDIS_REST_TOKEN` | Upstash Redis のトークン | Upstash コンソール |

その他の変数（`APP_MODULE` / `HOST` / `PORT`）はデフォルトのままで構いません。

## セットアップ・起動

### macOS / Linux

```bash
# 仮想環境の構築
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt

# 開発サーバーの起動
PYTHONPATH=src uvicorn app.main:app --reload
```

### Windows (PowerShell)

```powershell
# 仮想環境の構築
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
py -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt

# 開発サーバーの起動
$env:PYTHONPATH="src"
uvicorn app.main:app --reload --host 0.0.0.0
```

起動後、[http://localhost:8000](http://localhost:8000) でアクセスできます。  
Swagger UI（自動生成ドキュメント）は [http://localhost:8000/docs](http://localhost:8000/docs) で確認できます。

## デプロイ

Render.com にデプロイしています。フロントエンドからは本番環境のエンドポイントに自動で接続されます。
