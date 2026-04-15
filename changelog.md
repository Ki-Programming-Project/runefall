# RUNEFALL — 変更履歴

---

## v1.7（最新）

### 機能追加

- **実績ゲーム内効果（achEffects）全実装** — xpBonus / initGold / restBonus / bossHealBonus / bossGold / atkUpMaxLv 等。buildStartDeck（闇・土 power+1・デッキ+1枚）、resolveSpell（Holy Light系回復+3）、calcScore（goldBonusRate・turnBonusRate・easyScoreBonus）、openReward（ボス撃破Gold+30）、openShop（価格-1G）に反映
- **VOID難易度追加** — Rank6解放 または HARDクリア実績（clear_hard）で先行解放
- **運命の交差点イベント実装** — Rank解放後に出現。2択強化選択
- **Rank7特別演出** — タイトル画面の金色・大量星パーティクル

### バグ修正

- **Android操作不能バグ修正** — 演出Canvas（title / gameover / clear）の `touch-action:none` を `pointer-events:none` に変更

### 改善

- **キャッシュ対策** — APP_VERSION による自動リロード。更新時は APP_VERSION 値変更のみで全端末に新版が配信される
- **デッキ選択画面** — page-header / scroll / footer 構造に変更
- **EASY説明文** — 「Gold多め」を追加
- **チュートリアルBOX** — z-index を 1001 に変更

---

## v1.6

### 機能追加

- **RF.Rankモジュール新設** — META_UNLOCKS廃止・ランクシステムに一本化。RANKS（7段階 + 将来候補 Rank8〜10 をコメントで保持）。getCurrentRank / getRankTitle / checkRankUp / getRankEffects
- **実績システム30種に拡張** — ACHIEVEMENTS に xp フィールド追加
- **実績画面をカテゴリ別6タブ構造に変更** — ランク / 進行 / コンボ / 戦略 / デッキ / スコア
- **スタートデッキ選択制** — 4種 → 6種

### 改善

- **タイトル画面** — Rank1 から称号を表示
- **XPバー幅** — max-width:480px でコンテンツ幅と統一
- **calcMetaXP** — 1/4規模に削減・実績ボーナスXP付与

### バランス調整

- **ボスバランス再調整** — maxHp:90 / atk:10 / ボス前 HP+50

### バグ修正

- **openDeck のショップ判定** — G.currentRoom 参照に修正

---

## v1.4

### バグ修正

- **戦闘Canvas・マップCanvas 0サイズ問題** — `showScreen()` 直後の `getBoundingClientRect()` がレイアウト未確定でサイズ0を返す問題を修正。`requestAnimationFrame` で1フレーム遅延させてサイズ確定後に初期化
- **敵絵文字の非表示** — `ctx.fillText` 前に `fillStyle` が未設定のため背景グラデーションが引き継がれ絵文字が透明になっていた問題を修正。`ctx.fillStyle='white'` を明示設定
- **ゲームオーバー画面のScript error** — HTML刷新時に削除された `gameover-stats` 要素への参照がJS側に残存。該当行を削除
- **Canvas内の敵HPバー削除** — ヘッダー右上に敵HP表示が既存のため、Canvas内の重複表示を削除

### 改善・機能追加

- **titleRaf残存問題の解消** — `showScreen()` 冒頭で `titleRaf`・`battleRaf`・`goRaf`・`clearRaf`・`shakeRaf` の全RAFを一括キャンセル。画面遷移タイミングによる複数ループ起動を根本的に防止
- **デッキ確認→ショップショートカット** — デッキ確認画面フッターに「🏪 ショップへ」ボタンを追加。現在のフロアがショップ部屋（未クリア）の場合のみ表示
- **再挑戦の難易度引き継ぎ確認** — `currentDifficulty` で前回難易度を引き継ぐ設計を意図通りとして確認

### 演出強化（フェーズC完了）

- **画面シェイク** — 全攻撃に画面シェイクを追加。通常敵(強度3/150ms)、ボス通常(4/200ms)、竜の咆哮(6/350ms)、激怒(8/400ms)、炎の息吹(10/500ms) と脅威度に比例した演出
- **属性別パーティクルカラー** — 6属性それぞれ3色グラデーション（炎:赤橙系、水:青系、風:緑系、土:黄土系、光:白金系、闇:紫系）
- **コンボ時パーティクル増量** — コンボ発動時50粒子、基本攻撃時20粒子に差別化

---

## v1.3

### 緊急修正（v1.3の正常動作に必要だった6件）

- `openReward` 関数宣言の欠落補完
- `skipTurn` 関数の重複定義削除
- 難易度ボタン3種（`btn-easy`・`btn-normal`・`btn-hard`）のイベント登録
- イベント画面ボタン2種（`btn-event-accept`・`btn-event-decline`）の登録
- クリア画面へのスコアボード接続（`saveScore(true)` / `renderScoreBoard`）
- `onScreenEnter` でのタイトルベストスコア更新追加

### バグ修正

- `hp_up`・`gold_up`・`hand_up` の強化計算基準を難易度別 `diffConf` 参照に修正（NORMAL基準80のハードコードを解消）
- ボス激怒時のATK計算を `G.enemy.atk * 1.8`（難易度倍率適用済み）基準に修正
- イベント部屋のマップアイコンを `⚡` に設定（従来は `?` 表示）
- HARDデッキ上限チェックを報酬選択時・ショップ購入時に実装

---

## v1.2

### 実装（優先度：低）

- ボス「魔竜」に特殊行動3種を実装（HP40%以下で激怒・3ターンごとに炎の息吹・5ターンごとに竜の咆哮）
- ボスパラメータを強化（HP110、`BOSS_DEF` 定数として分離）
- Web Audio APIによるSE 8種（cast・combo・heal・hit・boss・special・victory・gameover）
- 初回プレイ時のみのチュートリアル5ステップ（localStorage管理）
- ボスアラート表示要素（`#boss-alert`）を戦闘画面に追加

---

## v1.1

### 実装（優先度：中）

- デッキ確認画面（属性別枚数・構成比率バー・コンボ到達可否ヒント）
- ショップをタブ式に改修（タイル購入・ステータス強化の2タブ）
- ステータス強化5種（体力強化・攻撃強化・回復強化・財宝の加護・手札拡張）
- 強化効果をゲーム全体に反映（atkBonus・healBonus・goldRate・handSize）
- マップヘッダーにデッキ枚数を表示

---

## v1.0

### 実装（優先度：高）

- 難易度カーブ全面再設計（F4以降クリア不可能問題の解消）
- 敵出現ロジックをフロア固定インデックスから重み付き確率抽選に変更
- ゲームオーバー画面にスコアボードを追加（到達フロア・Gold・デッキ枚数）
- マップヘッダーにGold表示を追加

### コアゲーム初期実装

- 全12画面（title・howto・map・battle・reward・shop・deck・event・rest・treasure・gameover・clear）
- ルーンタイル6種・コンボ呪文11種・敵5種
- 8フロアランダムダンジョン生成（enemy/shop/treasure/rest/event/boss）
- Canvas DPR対応
- 難易度選択3種・スコアシステム・ランダムイベント4種

---

## 主要バグ修正の記録

### タッチ操作不全（根本解決）

`body` への `touch-action:none` + `touchstart + preventDefault` の組み合わせが原因。最終解決策：body から `touch-action:none` 除去・`touchstart + preventDefault` を全廃し `onclick` のみに統一・全画面管理を `position:absolute` / `opacity` 方式から `display` 切り替えのみに変更。

### Canvas によるタッチ遮蔽

CSS の `canvas { position:absolute; inset:0 }` 共通スタイルがすべての Canvas をフルスクリーンに拡張し、ボタンのタッチを吸収していた。`#title-canvas`・`#gameover-canvas`・`#clear-canvas` の3要素のみに個別適用する方式に変更。

### Canvas 0サイズ初期化

`showScreen()` で `display:flex` 設定直後に `getBoundingClientRect()` を呼ぶとサイズが `0` になる。`requestAnimationFrame` で1フレーム遅延させることで解決。

### ENEMIES 定数の宣言欠落

`str_replace` 操作で `const ENEMIES = [` 宣言行が誤って削除されJS構文エラーが発生。以降は `str_replace` 前に必ず `view` で対象箇所を確認するルールを確立。
