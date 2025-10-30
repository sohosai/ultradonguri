# Ultradonguri

超正統派テロップ送出システムのデプロイ版

- サーバ (ultradonguri-server)
- コントローラ (ultradonguri-controller)

がすべて1つのcomposeにまとまっている統合環境です。

## システム構成

```
[ユーザー]
  ├─ :8080 → server (Go) - API/WebSocket/Swagger
  └─ :5173 → client (Vue.js) - コントローラーUI
```

- **server** (ポート8080): OBS WebSocketと通信してシーン切り替えやテロップ表示を制御
- **client** (ポート5173): Webベースのコントローラーインターフェース

## 事前準備

### 1. OBSの設定

OBSでWebSocketサーバーを有効にしてください：
- ツール → WebSocket サーバー設定
- WebSocketサーバーを有効化
- ポート: 4455（デフォルト）
- パスワードを設定

### 2. 環境設定

`compose.yaml` の環境変数を編集してください：

```yaml
server:
  environment:
    # OBS WebSocketのパスワード（必須）
    PASSWORD: "your_obs_password"

    # OBSのシーン名（必要に応じて変更）
    NORMAL_SCENE_NAME: "Normal_Scene"
    MUTED_SCENE_NAME: "Muted_Scene"
    CM_SCENE_NAME: "CM_Scene"
```

## 起動方法

### 通常起動（GHCRのproductionイメージを使用）

```bash
cd ultradonguri/ultradonguri
docker compose up -d
```

起動後、以下のURLでアクセスできます：

- **コントローラーUI**: http://localhost:5173
- **サーバーAPI**: http://localhost:8080
- **WebSocket**: ws://localhost:8080/ws
- **Swagger UI**: http://localhost:8080/swagger

### ログの確認

```bash
docker compose logs -f
```

### 停止

```bash
docker compose down
```

### データの永続化

シーンとテロップのバックアップデータは `./server-data` ディレクトリに保存されます。

## 開発者向け情報

### イメージの更新

最新のイメージを取得する場合：

```bash
docker compose pull
docker compose up -d
```

### 使用するイメージのバージョン

- **production（デフォルト）**: mainブランチからビルドされた本番用イメージ
  - `ghcr.io/sohosai/ultradonguri-server:production`
  - `ghcr.io/sohosai/ultradonguri-controller:production`

- **develop**: developブランチからビルドされた開発用イメージ
  - タグを `:develop` に変更して使用

### トラブルシューティング

#### OBSに接続できない

- OBSのWebSocketサーバーが有効になっているか確認
- `compose.yaml` の `PASSWORD` が正しいか確認
- Macの場合は `ADDRESS: host.docker.internal:4455` で接続可能
- Linuxの場合は `--network host` またはホストのIPアドレスを指定

#### ポートが使用中

ポート8080または5173が既に使用されている場合、`compose.yaml` のポートを変更してください：

```yaml
server:
  ports:
    - "8081:8080"  # 8081番ポートに変更

client:
  ports:
    - "5174:5173"  # 5174番ポートに変更
```

#### CORS エラーが発生する

コントローラーからサーバーへのAPIリクエストでCORSエラーが発生する場合は、サーバー側のCORS設定を確認してください。現在は `CONTROLLER_ADDRESS` 環境変数で許可するオリジンを設定しています。
