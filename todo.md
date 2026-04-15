> ⚠️ **廃止済み** — 内容は `spec.md（§10 改善課題）および changelog.md` を参照してください。このファイルは更新されません。

# RUNEFALL — TODO・課題管理

最終更新: 2026-03-19（v1.7）

---

## 未解決（保留・将来候補）

- PWA対応（manifest.json・Service Worker）← 保留中
- フロア数拡張検討（8→10〜12）
- 新敵の追加（後半フロアの多様性向上）
- 案C：呪い・祝福システム
- 案D：ミニボス追加（F4・F6）
- ランク拡張（Rank8〜10候補をコメントで保持済み）

---

## 完了済み

### v1.7

- ✅ 実績ゲーム内効果（achEffects）全実装
  - xpBonus/initGold/restBonus/bossHealBonus/bossGold/atkUpMaxLv等
  - buildStartDeck（闇・土power+1・デッキ+1枚）
  - resolveSpell（Holy Light系回復+3）
  - calcScore（goldBonusRate・turnBonusRate・easyScoreBonus）
  - openReward（ボス撃破Gold+30）
  - openShop（価格-1G追加）
- ✅ VOID難易度追加（Rank6解放 or HARDクリア実績で先行解放）
- ✅ 運命の交差点イベント実装（Rank解放後に出現・2択強化選択）
- ✅ Rank7特別演出（タイトル画面の金色・大量星パーティクル）
- ✅ Android操作不能バグ修正（演出CanvasをpointerEventsNoneに変更）
- ✅ キャッシュ対策（APP_VERSIONによる自動リロード）
  - 更新時はAPP_VERSIONの値変更のみで全端末に新版が配信される
- ✅ デッキ選択画面をpage-header/scroll/footer構造に変更
- ✅ EASY説明文に「Gold多め」を追加
- ✅ チュートリアルBOXのz-indexを1001に変更

### v1.6

- ✅ RF.Rankモジュール新設・META_UNLOCKS廃止・ランクシステムに一本化
  - RANKS（7段階 + 将来候補Rank8〜10をコメントで保持）
  - getCurrentRank / getRankTitle / checkRankUp / getRankEffects
- ✅ 実績システム30種に拡張（ACHIEVEMENTS xpフィールド追加）
- ✅ 実績画面をカテゴリ別6タブ構造に変更（ランク/進行/コンボ/戦略/デッキ/スコア）
- ✅ タイトル画面にRank1から称号を表示
- ✅ XPバー幅をmax-width:480pxでコンテンツ幅と統一
- ✅ calcMetaXP削減（1/4規模）・実績ボーナスXP付与
- ✅ スタートデッキ選択制（4種→6種）
- ✅ ボスバランス再調整（maxHp:90・atk:10・ボス前HP+50）
- ✅ openDeckのショップ判定をG.currentRoom参照に修正

### v1.5以前

（省略 - changelog.mdを参照）