> ⚠️ **廃止済み** — 内容は `spec.md（§4 システム構造 / §6 重要ロジック）` を参照してください。このファイルは更新されません。

# RUNEFALL — 技術アーキテクチャ仕様書

## 技術スタック

HTML / CSS / JavaScript（ES2020+）の単一ファイル構成です。外部ライブラリ・フレームワーク・ビルドツールは一切使用しません。唯一の外部依存はGoogle Fonts（Cinzel・Crimson Pro）の`@import`で、ネットワーク不在時はシステムフォントにフォールバックします。

```
index.html（1,583行）
  ├── <style>    CSS（変数・レイアウト・コンポーネント）
  ├── HTML       全12画面のDOM構造
  └── <script>   ゲームロジック全体（994行、'use strict'）
```

---

## 画面管理アーキテクチャ

### 設計方針（重要）

全画面は `display:none / flex` の切り替えのみで制御します。`position:absolute`・`opacity`・`transition` による重ね合わせ方式は採用しません。

```javascript
function showScreen(id) {
  // 全RAFを先にキャンセル（順序重要）
  if (titleRaf)  { cancelAnimationFrame(titleRaf);  titleRaf=null; }
  if (battleRaf) { cancelAnimationFrame(battleRaf); battleRaf=null; }
  if (goRaf)     { cancelAnimationFrame(goRaf);     goRaf=null; }
  if (clearRaf)  { cancelAnimationFrame(clearRaf);  clearRaf=null; }
  if (shakeRaf)  { cancelAnimationFrame(shakeRaf);  shakeRaf=null;
    document.getElementById('screen-battle').style.transform = '';
  }
  document.querySelectorAll('.screen').forEach(s => s.style.display = 'none');
  document.getElementById('screen-' + id).style.display = 'flex';
  G.screen = id;
  onScreenEnter(id);
}
```

### 画面一覧

| screen ID | 役割 | Canvas |
|-----------|------|--------|
| title     | タイトル・難易度選択 | ✅ 星空アニメーション |
| howto     | 遊び方説明 | ❌ |
| map       | ダンジョンマップ | ✅ ノード描画 |
| battle    | 戦闘 | ✅ 敵・パーティクル |
| reward    | タイル報酬選択 | ❌ |
| shop      | ショップ（タブ式） | ❌ |
| deck      | デッキ確認 | ❌ |
| event     | ランダムイベント | ❌ |
| rest      | 休憩所 | ❌ |
| treasure  | 宝箱 | ❌ |
| gameover  | ゲームオーバー | ✅ パーティクル |
| clear     | クリア | ✅ パーティクル |

---

## Canvas設計

### DPR対応

```javascript
// display:flex後は1フレーム遅延してサイズを確定してから初期化
requestAnimationFrame(() => {
  const dpr = window.devicePixelRatio || 1;
  const rect = canvas.getBoundingClientRect();
  canvas.width  = rect.width  * dpr;
  canvas.height = rect.height * dpr;
  ctx.scale(dpr, dpr);
  // 以降はCSS座標系で描画
});
```

### Canvas別責務・配置方針

**title-canvas / gameover-canvas / clear-canvas** は `position:absolute; inset:0` で画面全体を覆います。コンテンツ要素は `z-index:10` で前面表示します。

**battle-canvas / map-canvas** は `flex:1; min-height:0` でflexアイテムとして高さを確保します。`position:absolute` は使用しません。

### RAFライフサイクル管理

各Canvas のRAFハンドルはグローバル変数で管理します（`titleRaf`・`battleRaf`・`goRaf`・`clearRaf`・`shakeRaf`）。`showScreen()` 冒頭で全ハンドルを一括キャンセルします。新しいCanvasアニメーションを追加する場合は対応するRAF変数を `showScreen()` のキャンセルリストに必ず追加してください。

---

## タッチ操作設計

### 基本方針（変更禁止）

すべてのインタラクティブ要素は `onclick` のみを使用します。

```javascript
// ✅ 採用
el.onclick = () => handleAction();

// ❌ 禁止（iOS/EdgeでclickイベントのFireを阻害）
el.addEventListener('touchstart', e => { e.preventDefault(); handleAction(); });
```

`touch-action:manipulation` をすべてのボタン・タイル・インタラクティブ要素に付与します。Canvasのみ `touch-action:none` を個別設定します。

---

## ゲーム状態管理

```javascript
G = {
  screen, floor, difficulty, diffConf,
  player: { hp, maxHp, gold, atkBonus, healBonus, goldRate, handSize },
  upgradeLevels: { hp_up, atk_up, heal_up, gold_up, hand_up },
  deck, hand, selected,
  enemy: { ...params, hp, turn, enraged },
  enemyEffects,
  dungeon, currentRoom,
  animating, particles,
  mapRooms, mapCurrentIdx,
  totalTurns, spellComboBonus,
}
```

`animating` フラグは詠唱開始時に `true`、敵反撃処理完了後に `false` に戻す二重入力防止ミューテックスです。

---

## サウンドエフェクト

Web Audio APIを使用。AudioContextはユーザー操作後に遅延初期化してAutoplay Policy制限を回避します。すべての処理は `try-catch` で保護しSE失敗がゲームプレイに影響しません。

---

## 画面シェイク

戦闘画面の `#screen-battle` 要素全体に `style.transform = translate(x, y)` を適用し `requestAnimationFrame` ループで減衰させます。画面遷移時は `shakeRaf` をキャンセルして `transform` をリセットします。

---

## レイアウトシステム

全画面を「固定ヘッダー・スクロール領域・固定フッター」の3分割構造で統一しています。

```
.screen (display:flex; flex-direction:column; height:100%)
  ├── .page-header  (flex-shrink:0)  ← 固定
  ├── .page-scroll  (flex:1)         ← スクロール可能
  │    └── .page-body
  └── .page-footer  (flex-shrink:0)  ← 固定
```

---

## 永続化

localStorageを使用。セッション状態のセーブは非対応（ローグライクの原則）。

| キー | 内容 |
|------|------|
| runefall_best_easy | EASYベストスコア |
| runefall_best_normal | NORMALベストスコア |
| runefall_best_hard | HARDベストスコア |
| runefall_total_runs | 総プレイ回数 |
| runefall_tutorial_done | チュートリアル完了フラグ（"1"）|