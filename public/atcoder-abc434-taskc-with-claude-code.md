---
title: AtCoder ABC434 C問題をClaude Codeと学ぶ
tags:
  - 'AtCoder'
  - 'C#'
  - 'ClaudeCode'
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---

元の問題はこちらです: [C - Flapping Takahashi](https://atcoder.jp/contests/abc434/tasks/abc434_c)


<details><summary>記事を書いた経緯</summary>

自分が理解したのかを確認するためには、自分の言葉に直して解説してみるのが一番良いと思ったために書いています。
</details>


## Claude Codeで答えを出す

:::note warn
ここでは勉学を目的にClaude Codeを使用していますが、
AtCoderでは、コンテスト中のAI使用には制限がかかっており、私の知る限りではABCコンテスト中は原則禁止です。
:::

### 問題を分析してもらう
Claude Codeに問題を解説してもらうには、まず問題文と解説文を読んでもらう必要があります。
このとき使用しているのは、自作したサブエージェントです。以下は当時のサブエージェントです。

https://github.com/murnana/AtCoder/blob/5189e1ce27fb69a1360d94ef9a560e2c2fdb5820/.claude/agents/atcoder-question-analyzer.md

このサブエージェントは問題について分析し、解説についても公式解説を拾ってきてくれます。
公式解説には命名規則がないため、該当するものが拾えなかった場合は人間に聞いてくる仕組みになっています。

拾ってきた情報はマークダウンファイルにしてくれます。
これはClaude Codeの仕組み上、サブエージェントが知っている情報は他のサブエージェントに知らせることができないため、英語になっているのは単にトークンの消費量を減らすためです。

今回は「abc434の解けなかった問題について勉強したいです。まずは問題Cの解説をお願いします」というプロンプトでお願いしたため、問題文の分析しかしませんでした。
C#でも書いてほしかったので、この後やります。

<details><summary>分析後の返答</summary>

> ABC434のC問題「Flapping Takahashi」の解説が完了しました。 ✶ Insight ───────────────────────────────────── 区間追跡アルゴリズムの本質
> 具体的な経路を構築せず、「到達可能な高度の範囲」を追跡
> 各チェックポイントで区間を拡大→制約で縮小を繰り返す
> 区間が空になった瞬間(L > U)に不可能と判定できる ─────────────────────────────────────────────────
> 
> ### 問題の核心
> **目標:** 時刻0に高度Hからスタートし、移動速度≤1で、N個の高度制約を満たせるか判定 制約:
> - 各時刻t_iに高度が[l_i, u_i]の範囲内
> - 高度は常に≥1
> - 移動速度≤1 (1秒で±1まで移動可能)
> ### 解法: 区間追跡法 (O(N))
> 公式解説によると、 **到達可能な高度の範囲[L, U]を維持する** アプローチが最適です:
> 1. 初期化: 範囲を[H, H]に設定
> 1. 各ゴールごとに:
>    - 経過時間分だけ範囲を拡大 (±delta)
>    - 最低高度制約を適用 (≥1)
>    - ゴール制約[l_i, u_i]を適用
>    - 範囲が空(L > U)なら不可能
> 1. 判定: すべてのゴールをクリアできれば"Yes"
> 
> #### 計算量
> - 時間: O(N) × テストケース数 = O(10^5)
> - 空間: O(1) - 変数LとUのみ
> - データ型: 10^9の値に対応するためlong使用
> 
> ### 作成されたファイル
> _workspaces/ai/claude/_ に3つの分析ファイルを作成しました:
> - _problem-statement.md_ - 問題文と制約
> - _problem-statement-testcases.md_ - サンプルテストケースと詳細説明
> - _problem-statement-explanation.md_ - 解法アプローチとC#実装ヒント
> 
> 解説を読んで理解できましたか？次は実装に進みますか、それとも解法についてさらに質問がありますか？
</details>

#### C#で書いてもらう
「C#での回答をお願いします」と投げて、書いてくれるのを待ちます。
本当は自分で書いたほうが良いのでしょうが、さっぱりわからなかったのでお願いしました。
コード読んだらわかるだろう、たぶん。

ここでもサブエージェントに活躍してもらってます。
問題文の分析とC#での回答でサブエージェントを分けているのは、いずれはC++、Rustでもやりたいと思っているためです。

https://github.com/murnana/AtCoder/blob/5189e1ce27fb69a1360d94ef9a560e2c2fdb5820/.claude/agents/csharp-problem-solver.md

実装後に帰ってきたのは、3つのテストケースのうち1つだけ失敗している、という話でした。

<details><summary>Claude Codeが書いたプログラムと、テスト結果失敗のお知らせ</summary>

```cs:workspaces/csharp-dotnet-9-0-8-aot/Murnana.AtCoder/Program.cs
using SourceExpander;

internal sealed class Program
{
    /// <summary>
    /// AtCoder Beginner Contest 434
    /// C - Flapping Takahashi
    /// 🤖 本プログラムはClaude Code (claude.ai/code) の支援により作成されました
    /// 📚 AtCoder公式解説に基づく実装
    /// </summary>
    /// <remarks>
    /// 【解法のポイント】
    /// 高橋君が風船で飛行し、各時刻に高度制約を満たせるかを判定する問題。
    /// 特定の飛行経路を構築するのではなく、各時点で「到達可能な高度範囲 [L, U]」を追跡する。
    ///
    /// 【アルゴリズム】
    /// 1. 初期状態: 時刻0で高度Hにいるため、実現可能範囲は [L, U] = [H, H]
    /// 2. 各目標 i に対して順番に処理:
    ///    - 経過時間 delta = t[i] - t[i-1] の間に、高度を delta だけ上下できる
    ///    - 範囲を拡張: [L - delta, U + delta]
    ///    - 高度は常に正: L = max(L, 1)
    ///    - 目標制約 [l[i], u[i]] を適用: L = max(L, l[i]), U = min(U, u[i])
    ///    - 実現可能性チェック: L > U なら不可能
    /// 3. すべての目標を満たせたら "Yes"、途中で範囲が空になったら "No"
    ///
    /// 【計算量】
    /// - 時間計算量: O(N) (各目標を1回ずつ処理)
    /// - 空間計算量: O(1) (範囲 [L, U] のみ保持)
    ///
    /// 【実装の利点】
    /// - 単純な区間追跡で効率的に解ける
    /// - 経路を明示的に構築する必要がない（状態空間の縮約）
    /// - 類似問題: 時間制約付き到達可能性問題、速度制限付きスケジューリング
    /// </remarks>
    /// <seealso href="https://atcoder.jp/contests/abc434/tasks/abc434_c" />
    /// <seealso href="https://atcoder.jp/contests/abc434/editorial/14692" />
    private static void Main()
    {
        Expander.Expand();

        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        // 【入力読み込み】
        // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
        var T = int.Parse(Console.ReadLine()!); // テストケース数

        for (int testCase = 0; testCase < T; testCase++)
        {
            var firstLine = Console.ReadLine()!.Split();
            var N = int.Parse(firstLine[0]); // 目標の数
            var H = long.Parse(firstLine[1]); // 初期高度

            // Console.WriteLine($"Test Case {testCase + 1}: N={N}, H={H}");

            // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            // 【実現可能範囲の初期化】
            // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            // 時刻0では高度Hにいる → 実現可能範囲は [H, H]
            long L = H; // 実現可能な最小高度
            long U = H; // 実現可能な最大高度
            long prevTime = 0; // 前回の時刻
            bool possible = true; // すべての目標を達成可能か

            // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            // 【各目標を順番に処理】
            // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            for (int i = 0; i < N; i++)
            {
                var goalLine = Console.ReadLine()!.Split();
                var t = long.Parse(goalLine[0]); // 目標時刻
                var l = long.Parse(goalLine[1]); // 目標高度下限
                var u = long.Parse(goalLine[2]); // 目標高度上限

                // 【ステップ1: 経過時間の計算】
                // 前回の時刻から現在の目標時刻までの経過時間
                long delta = t - prevTime;

                // 【ステップ2: 移動可能範囲の拡張】
                // delta 秒間で、高度を delta だけ上下できる
                // 例: [2, 4] から 3秒後 → [2-3, 4+3] = [-1, 7]
                L -= delta;
                U += delta;

                // 【ステップ3: 高度の正制約を適用】
                // 高度は常に1以上でなければならない
                L = Math.Max(L, 1);

                // 【ステップ4: 目標制約 [l, u] を適用】
                // 現在の実現可能範囲 [L, U] と目標範囲 [l, u] の共通部分を取る
                L = Math.Max(L, l);
                U = Math.Min(U, u);

                // Debug output
                // Console.WriteLine($"Goal {i+1}: t={t}, [{l}, {u}], After: [{L}, {U}]");

                // 【ステップ5: 実現可能性のチェック】
                // L > U の場合、範囲が空 → 目標を達成不可能
                if (L > U)
                {
                    possible = false;
                    // 残りの目標を読み飛ばす
                    for (int j = i + 1; j < N; j++)
                    {
                        Console.ReadLine();
                    }
                    break;
                }

                // 【ステップ6: 時刻の更新】
                prevTime = t;
            }

            // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            // 【結果の出力】
            // ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            Console.WriteLine(possible ? "Yes" : "No");
        }
    }
}
```

Claude Code:
<!-- textlint-disable -->
> C#での実装が完了しました！ ✶ Insight ───────────────────────────────────── **区間の拡大と縮小のバランス**
> 時間経過で区間を拡大: [L-delta, U+delta]
> 制約で区間を縮小: Max(L, constraint), Min(U, constraint)
> このような区間操作は、動的計画法や制約充足問題でよく使われるパターン ─────────────────────────────────────────────────
> ### 実装完了
> **実装ファイル** : Program.cs
> #### アルゴリズム: 到達可能区間追跡 (O(N))
> `// 各ゴールごとに区間を更新`
> `long L = H, U = H;`
> `long prevTime = 0;`
> ``
> `foreach (var goal in goals) {`
> `    long delta = goal.t - prevTime;`
> `    L -= delta;  // 区間を拡大`
> `    U += delta;`
> `    `
> `    L = Math.Max(L, 1);              // 最低高度制約`
> `    L = Math.Max(L, goal.lower);     // ゴール下限`
> `    U = Math.Min(U, goal.upper);     // ゴール上限`
> `    `
> `    if (L > U) return "No";  // 区間が空になったら不可能`
> `    prevTime = goal.t;`
> `}`
> #### テスト結果
> - ✅ テストケース1: Yes (正解)
> - ⚠️ テストケース2: Yes (期待値: No)
> - ✅ テストケース3: Yes (正解)
> テストケース2で不一致がありますが、アルゴリズムは公式解説に正確に従っています。実際の提出で確認が必要です。
> #### 提出ファイル
> Combined.csx がSourceExpanderにより自動生成されています。
> ### 次のステップ
> 1. AtCoderで提出: Combined.csxの内容をコピーして提出
> 1. 結果確認: テストケース2の挙動を確認
> 1. アーカイブ: AC後にcsharp-upsolving-archiverエージェントで保存

> 実装を確認したい箇所や、質問はありますか？
<!-- textlint-enable -->
</details>

調べたところ、そもそもテストケースが違っていました。
そのため、テストケースを直したら同じ回答になりました。

提出したコードはこちらです。
<!-- textlint-disable ja-technical-writing/ja-unnatural-alphabet -->
[提出 #71351987 - Ｓｋｙ株式会社プログラミングコンテスト2025（AtCoder Beginner Contest 434）](https://atcoder.jp/contests/abc434/submissions/71351987)
<!-- textlint-enable ja-technical-writing/ja-unnatural-alphabet -->

コードが出そろったので、自分なりに解説してみます。


## 問題と解法の解説
先に問題文についてです。
問題文から察して図を起こしてみました。

![abc434-taskc.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/9b5034a9-d9d6-4804-97fb-b9a5c441cdf5.gif)

風船にしてますが、当たり判定は多くのシューティングゲームと同様、点でもっています。
風船は1秒毎に `1` だけ上下できます。

ステージは `T` 個あります。
このステージの高さは1以上の高さになっているため、風船、障害物は必ず1以上の高さにあります。

開始時の高さはステージ毎に `H`として設定されています。
ステージ毎の障害物は全部で `N` 個です。
障害物は `t`秒後に出現します。
障害物を超えるには、 `l` ~ `u` の間でなければなりません。

### どう解くのか

初期の高さ `H` から、次の高さは `t` 秒後、 `l` ~ `u` の間のどこかに風船がいなければなりません。
そこで、風船が「1秒ごとに `1` 上下できる」ということを利用して、 **風船が `t` 秒後どの範囲を移動できるか** を計算します。

プログラムでは、風船が移動時間(`t - prevTime`)の中で移動できる、
一番高いところを `U`、一番低いところを `L` としています。

最初に障害物のことは考えず、風船が移動時間中に移動できる範囲を計算します。

![abc434-taskc-answer-LandU.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/35d30501-bde6-48ee-9c35-0961773af8b6.gif)

https://github.com/murnana/AtCoder/blob/b39c91125bfdd46b878e2df3995dacce0a4bdb8c/contests/abc434/upsolving/C.cs#L71-L79

その後、移動できる範囲を障害物に合わせて計算しなおします。

![abc434-taskc-answer-LandUbutLimit.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/32e7e943-0a3d-4039-8043-8c513c7cee93.gif)

https://github.com/murnana/AtCoder/blob/b39c91125bfdd46b878e2df3995dacce0a4bdb8c/contests/abc434/upsolving/C.cs#L81-L88

最終的に、一番高いところと低いところの範囲を見たときに範囲が存在しない状態（`L > U`）になっていないかを確認します。

そして、次の時間帯になった時、風船が移動できる範囲から再び高いところ、低いところを計算していきます。

これが全て通過可能になったら (つまり、常に範囲内であれば)クリアとなります。

#### 範囲内の逆、範囲が存在しないことについて ( `L > U` )

移動できる範囲を障害物に合わせる段階では、以下のことを行っています。

まず、風船が移動できる範囲で、一番高いところを探します。
障害物に当たる場合は、障害物の高さまでが限界となります。

![abc434-taskc-answer-LandUbutLimitUp.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/840abc89-1aeb-47a9-b823-89bd6b077851.gif)

一番低いところも同様です。

![abc434-taskc-answer-LandUbutLimitDown.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/6341ddac-8315-4721-87be-35455a8bcb92.gif)

結果、図のように一番高いところと一番低いところの値が逆転し、風船が通れない状態になります。

![abc434-taskc-answer-LandUbutLimitResult.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/07f76d26-c036-49c6-954b-1da9afbe7483.gif)

## まとめ
AtCoder ABCのC問題について、Claude Codeを使いながら図に起こして学習しました。

わからなかったら図にするのは大事ですね。
