# microball ZMK設定

![ビルドステータス](https://github.com/k-makoto/zmk-config-microball/actions/workflows/build.yml/badge.svg)

**microball** 分割キーボード（トラックボール搭載）用のZMKファームウェア設定です。

## 🖥️ ハードウェア

- **コントローラー**: Seeeduino XIAO BLE (nRF52840)

- **レイアウト**: 40キー分割レイアウト（親指クラスター付き）

- 機能

  :

  - PMW3610光学式トラックボールセンサー
  - ロータリーエンコーダー対応
  - ワイヤレス接続（Bluetooth）
  - 日本語レイアウト最適化

## 🔧 特徴

### キーボードレイアウト

- **40キー分割設計**（人間工学的な親指クラスター）
- **日本語キーボード対応**（OS設定との互換性）
- **6レイヤー構成**（マウス、スクロール、ファンクションレイヤー含む）
- **コンボキー**による生産性向上

### トラックボール統合

- **PMW3610センサー**（デフォルト400 CPI）
- **自動マウスレイヤー**切替（700msタイムアウト）
- **スクロールレイヤー**による精密ナビゲーション
- **スマートアルゴリズム**による動作最適化

### 接続性

- **ZMK Studio**対応（リアルタイム設定変更）
- **Bluetooth Low Energy**によるワイヤレス動作
- **分割キーボード**通信（BLE経由）

## 📥 インストール

### 方法1: ビルド済みファームウェアのダウンロード

1. [Actionsタブ](https://github.com/k-makoto/zmk-config-microball/actions)にアクセス
2. 最新の成功したビルドをクリック
3. ファームウェアZIPファイルをダウンロード
4. 解凍して適切な.uf2ファイルを書き込み：
   - `microball_L-seeeduino_xiao_ble-zmk.uf2` → 左半分
   - `microball_R-seeeduino_xiao_ble-zmk.uf2` → 右半分
   - `settings_reset-seeeduino_xiao_ble-zmk.uf2` → 設定リセット

### 方法2: ローカルでビルド

```bash
# このリポジトリをクローン
git clone https://github.com/k-makoto/zmk-config-microball.git
cd zmk-config-microball

# ZMKワークスペースを初期化
west init -l config/
west update
west zephyr-export

# ファームウェアをビルド
west build -d build/left -b seeeduino_xiao_ble -- -DSHIELD=microball_L
west build -d build/right -b seeeduino_xiao_ble -- -DSHIELD=microball_R
```

## 🔌 書き込み手順

1. **ブートローダーモード**: XIAO BLEのリセットボタンを2回押し
2. **USBドライブとしてマウント**: `XIAO-SENSE`として認識される
3. **ファームウェアをコピー**: `.uf2`ファイルをマウントしたドライブにドラッグ&ドロップ
4. **自動再起動**: 新しいファームウェアで再起動

**推奨書き込み順序:**

1. 左半分を先に書き込み
2. 右半分を後に書き込み
3. Bluetooth経由で両半分をペアリング

## 🎯 レイヤー構成

| レイヤー | 用途                 | 起動方法                       |
| -------- | -------------------- | ------------------------------ |
| 0        | ベース（デフォルト） | デフォルトレイヤー             |
| 1        | ファンクション/記号  | スペースキー長押し             |
| 2        | 数字/Fキー           | かなキー長押し                 |
| 3        | ナビゲーション/矢印  | 英数キー長押し                 |
| 4        | マウス               | トラックボール使用時自動切替   |
| 5        | スクロール           | トラックボールスクロールモード |
| 6        | 設定/Bluetooth       | Enterキー長押し                |

## 🖱️ トラックボール設定

### マウス設定

- **デフォルトCPI**: 400
- **スクロールティック**: 16
- **自動マウスタイムアウト**: 700ms
- **ポーリングレート**: 125Hz

### カスタマイズ

`microball_R.conf`を編集してトラックボールの動作を調整：

```conf
CONFIG_PMW3610_CPI=400                    # マウス感度
CONFIG_PMW3610_AUTOMOUSE_TIMEOUT_MS=700   # 自動レイヤータイムアウト
CONFIG_PMW3610_SCROLL_TICK=16             # スクロール感度
```

## 🇯🇵 日本語レイアウト対応

この設定には包括的な日本語キーボードマッピングが含まれています：

```c
#define JP_DQUOTE       AT                // "
#define JP_AMPERSAND    CARET             // &
#define JP_QUOTE        AMPERSAND         // '
// ... microball.keymapに追加のマッピング
```

**推奨OS設定**: 日本語IMEを有効にし、ハードウェアキーボードをJIS配列に設定してください。

## 🔄 Bluetooth管理

### 新しいデバイスのペアリング

1. レイヤー6に移行（Enterキー長押し）
2. キー1-5を押してプロファイルスロットを選択
3. 自動的にペアリングモードに入ります

### Bluetooth設定のクリア

- **単一プロファイル**: レイヤー6のBT_CLR
- **全プロファイル**: レイヤー6のBT_CLR_ALL
- **工場出荷時リセット**: `settings_reset`ファームウェアを書き込み

## 🛠️ カスタマイズ

### キーマップの変更

1. `config/microball.keymap`を編集
2. 変更をコミットして自動ビルドを起動
3. Actionsタブからアップデートされたファームウェアをダウンロード

### 機能の追加

この設定は以下をサポート：

- カスタムマクロとビヘイビア
- 追加のコンボキー定義
- レイヤー固有のエンコーダー機能
- ZMK Studioリアルタイム編集

## 📂 リポジトリ構造

```
config/
├── microball.keymap           # メインキーマップ設定
├── microball.json             # 物理レイアウト定義
├── microball.dtsi             # ハードウェア定義
├── microball_L.overlay        # 左半分設定
├── microball_R.overlay        # 右半分設定
├── microball_L.conf           # 左半分設定
├── microball_R.conf           # 右半分設定
├── west.yml                   # ZMK依存関係
└── build.yaml                 # GitHub Actionsビルドマトリックス
```

## 🤝 コントリビューション

1. このリポジトリをフォーク
2. フィーチャーブランチを作成
3. 変更を実施
4. 自動ビルドシステムでテスト
5. プルリクエストを送信

## 📚 リソース

- [ZMKドキュメント](https://zmk.dev/)
- [ZMK Studio](https://zmk.dev/docs/features/studio)
- [Seeeduino XIAO BLE](https://wiki.seeedstudio.com/XIAO_BLE/)
- [PMW3610トラックボールドライバー](https://github.com/Yu1812/zmk-pmw3610-driver)

## 📄 ライセンス

この設定はMITライセンスの下でリリースされています。コンポーネント固有のライセンスについてはハードウェアドキュメントを参照してください。

------

**楽しいタイピングを！** 🎊 問題が発生した場合は、[Issues](https://github.com/k-makoto/zmk-config-microball/issues)セクションを確認するか、新しいissueを作成してください。
