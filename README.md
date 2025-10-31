# Ultradonguri

超正統派テロップ送出システムのデプロイ版

- サーバ (ultradonguri-server)
- コントローラ (ultradonguri-controller)

がすべて1つのcomposeにまとまっている統合環境です。

## システム構成

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

`events.json`を作成してください

## 起動方法

### 通常起動（GHCRのproductionイメージを使用）

```bash
cd ultradonguri/ultradonguri
docker compose up
```

起動後、以下のURLでアクセスできます：

- **コントローラーUI**: http://localhost:5173
- **サーバーAPI**: http://localhost:8080
- **WebSocket**: ws://localhost:8080/ws

### サーバーの持つデータの管理

シーンとテロップのバックアップデータは `./server-data` ディレクトリに保存されます。

## 開発者向け情報

### 使用するイメージのバージョン

- ビルドされた最新イメージ
  - `ghcr.io/sohosai/ultradonguri-server:latest`
  - `ghcr.io/sohosai/ultradonguri-controller:latest`
