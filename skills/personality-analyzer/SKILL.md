---
name: personality-analyzer
description: チャット内のユーザー発言を分析し、HEXACO・MBTIによる性格診断とポケモン選出を行う。「性格診断して」「このチャットから性格を分析」「HEXACOスコアを出して」「MBTIを判定して」などのリクエストで使用。言語指定も可能（例：「性格診断して lang:en」「analyze my personality lang:en」）。分析対象は現在のチャット内のユーザー発言のみ（過去チャットは参照しない）。
---

# Personality Analyzer

チャット内のユーザー発言からHEXACO・MBTIスコアを算出し、YAML形式で出力する。

## トリガーと言語指定

- 基本: 「性格診断して」「HEXACOスコアを出して」「MBTIを判定して」
- 言語指定: `lang:xx` を付与（例: 「性格診断して lang:en」「analyze my personality lang:zh」）
- 言語指定がない場合はユーザーの主要使用言語で出力

## 分析プロセス

1. **発言収集**: スキル実行直前までのユーザー発言をすべて抽出
2. **信頼度算出**: 発言量・多様性から診断の信頼度（0-100）を算出
3. **言語決定**: 指定言語 or ユーザーの主要使用言語を特定
4. **チャット要約**: タイトル（簡潔）と要約文を生成（字数規定は「共通出力規約」節を参照）
5. **HEXACO分析**: 6因子それぞれのスコア（1.0-5.0）と根拠を算出
6. **MBTI判定**: 4つの軸それぞれの判定と根拠、最終タイプを決定
7. **ポケモン選出**: HEXACOプロファイルに基づき類似ポケモン3体を選出
8. **総評作成**: 全体的な性格傾向のまとめ

## 共通出力規約

- **timestamp**: skill 実行時の現在時刻を UTC 変換して ISO8601 形式で出力（例: `2026-04-23T03:16:00Z`）。システム時刻を直接取れない場合は、会話中の日時ヒントから推定する。
- **chat.summary 字数**: 出力 language に応じて以下を目安とする。内容密度を優先し、多少前後してよい。
  - CJK（日本語・中国語・韓国語など）: 100-180 字
  - 英語・その他欧州言語: 50-100 words
- **ポケモン名の表記**: 出力 language の公式表記に揃える（`ja` → 日本語名「ルカリオ」、`en` → 英語名「Lucario」、他言語も該当言語の公式表記）。
- **reasoning / general_evaluation / chat.title / chat.summary の言語**: 全て出力 language に統一する。

## MBTI キー名の対応

YAML の `MBTI` 節内のキー `M/B/T/I` は下記 4 軸を表す短縮名。`type` の 1 〜 4 文字目と一対一対応する:

| キー | 意味 | 取りうる type | 説明 |
|---|---|---|---|
| M | Mind | `E` / `I` | 外向 Extraversion / 内向 Introversion |
| B | Brain | `S` / `N` | 感覚 Sensing / 直観 iNtuition |
| T | Thinking | `T` / `F` | 思考 Thinking / 感情 Feeling |
| I | Implementing | `J` / `P` | 判断 Judging / 知覚 Perceiving |

最終 `type`（例: `INTJ`）は M, B, T, I の `type` 値を順に連結した文字列と一致させること。不整合があれば再判定する。

## 出力フォーマット

```yaml
timestamp: "2026-04-23T12:00:00Z" # ISO8601 UTC
language: "ja" # 出力言語（指定 or ユーザーの主要使用言語）
credibility: 75 # 診断の信頼度（0-100）。発言量・多様性に基づく
chat:
  title: "チャットタイトル"
  summary: >-
    チャット要約文（CJK 100-180字 / 英語 50-100 words）
HEXACO:
  H:
    score: 3.5
    reasoning: >-
      根拠
  E:
    score: 3.0
    reasoning: >-
      根拠
  X:
    score: 4.0
    reasoning: >-
      根拠
  A:
    score: 3.5
    reasoning: >-
      根拠
  C:
    score: 4.5
    reasoning: >-
      根拠
  O:
    score: 4.0
    reasoning: >-
      根拠
MBTI:
  type: "INTJ"
  reasoning: >-
    総合的な判定理由
  M:
    type: "I"
    reasoning: >-
      内向/外向の判定理由
  B:
    type: "N"
    reasoning: >-
      直観/感覚の判定理由
  T:
    type: "T"
    reasoning: >-
      思考/感情の判定理由
  I:
    type: "J"
    reasoning: >-
      判断/知覚の判定理由
Pokemon:
  - name: "ポケモン名1"
    reasoning: >-
      選出理由
  - name: "ポケモン名2"
    reasoning: >-
      選出理由
  - name: "ポケモン名3"
    reasoning: >-
      選出理由
general_evaluation: >-
  総評（性格傾向の全体的なまとめ）
```

## HEXACO因子

| 因子 | 名称 | 高スコアの特徴 | 低スコアの特徴 |
|------|------|----------------|----------------|
| H | Honesty-Humility | 誠実、謙虚、公正 | 自己顕示的、計算高い |
| E | Emotionality | 感情的、不安を感じやすい | 感情的に安定、独立的 |
| X | eXtraversion | 社交的、活発、自己主張 | 内向的、控えめ |
| A | Agreeableness | 寛容、柔軟、忍耐強い | 批判的、頑固 |
| C | Conscientiousness | 計画的、勤勉、慎重 | 衝動的、柔軟 |
| O | Openness | 創造的、知的好奇心旺盛 | 実用的、保守的 |

## 分析時の注意

- 中立的な発言が多い因子は中央値（3.0）付近に
- 極端なスコア（1.0-1.5, 4.5-5.0）は明確な根拠がある場合のみ
- ポケモン選出はHEXACOプロファイルとの性格的類似性で判断する（ゲーム内能力ではなく、性格・イメージ面で）

## credibility算出基準

| 発言数 | 内容の多様性 | credibility目安 |
|--------|--------------|-----------------|
| 1-3件 | 低 | 10-25 |
| 4-7件 | 低〜中 | 25-45 |
| 8-15件 | 中 | 45-65 |
| 16-30件 | 中〜高 | 65-80 |
| 31件以上 | 高 | 80-100 |

- 発言数だけでなく、感情表現・意見・価値観が現れる発言の有無も考慮
- 技術的な質問のみの場合は性格特性が現れにくいため、credibility を下げる
- **多様性の数え方**: 技術質問 / 感情表現 / 意見・価値観 / 対人エピソード / 趣味・学習対象 / 目標・計画 など異なる話題軸の数で判定。2 軸以下=低、3-4 軸=中、5 軸以上=高。
