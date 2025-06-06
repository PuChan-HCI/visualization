# GradCAMで深層学習モデルの判断根拠を可視化

深層学習は，画像認識や自然言語処理など，さまざまな分野で高い性能を示しており，幅広く活用されている．しかし，深層学習モデルは構造が非常に複雑であるため，なぜそのような判断を下したのかを人間が理解することは容易ではない．いわゆる「ブラックボックス」としての性質が課題となっている．

この問題に対応するため，近年ではXAI（Explainable AI：説明可能な人工知能）と呼ばれる技術が注目されている．XAIとは，機械学習モデルの判断根拠を人間にとって理解しやすい形で可視化・説明するための技術である．

XAIの代表的な手法の一つに，GradCAM（Gradient-weighted Class Activation Mapping）がある．GradCAMは，画像分類モデルが入力画像のどの部分に注目して判断を行っているかを視覚的に示すことができる．これにより，モデルの判断過程を直感的に理解することが可能となる．

[Source](https://www.codemajin.net/visualize-prediction-of-cnn-with-gradcam/)

## XAIが必要とされる理由
XAI（Explainable AI：説明可能なAI）は，以下のような観点からその重要性が指摘されている．
- 信頼性の向上：AIの判断理由を可視化・説明可能にすることで，ユーザはAIの出力に対してより高い信頼を寄せることができるようになる．
- 責任性の確保：AIが誤った判断を下した場合でも，その根拠や原因を特定することが可能となり，適切な修正や再学習が可能となる．
- 規制への対応：AIの利用に関する規制が各国で強化されつつある中，XAIはモデルの透明性を確保し，法的・倫理的な要件を満たす手段として有効である．

## XAIの活用例:
XAIの技術は，さまざまな応用分野で活用されており，以下のような事例が報告されている．
- 医療診断：診断支援AIにおいて，モデルの判断根拠を医師に提示することで，診断精度の向上や誤診の防止に寄与する．
- 金融分野：融資審査モデルの判断理由を顧客に説明することにより，透明性が向上し，顧客満足度の向上に繋がる．
- 自動運転：自律走行車の挙動について，なぜ特定の判断（例：停止や回避）を行ったのかを説明することで，ユーザの安心感やシステムの信頼性向上が期待される．

## CAM（Class Activation Map）について
まず，Grad-CAMの基盤となるアルゴリズムであるCAM（Class Activation Map）について解説する．CAMは，画像分類タスクにおける畳み込みニューラルネットワーク（CNN）に特化した説明可能なAI（XAI）の手法であり，モデルの判断根拠を視覚的に示すことが可能である．

CAMの動作は以下の3つのステップに分けられる．
1. 特徴の抽出：入力画像は，CNNの畳み込み層により処理され，各層で様々な空間的特徴が抽出される．その後，Global Average Pooling（GAP）層を通じて，各特徴マップの平均値が算出され，次の全結合層に入力される．
2. 重要度の計算：各特徴マップの重要度は，全結合層における重み係数により定まる．この重みは，学習過程で最適化され，それぞれの特徴が分類結果にどの程度寄与しているかを示す指標となる．
3. ヒートマップの作成：算出された特徴マップとその重要度に基づき，入力画像と同じサイズのヒートマップを生成する．ヒートマップにおいて明るい（高強度の）領域は，モデルがその領域に注目して判断を下したことを示している．

このようにCAMは，CNNベースの画像分類モデルにおける判断根拠の可視化を可能にすることで，モデルの挙動を直感的に理解する手段を提供する．

![GradCam](https://www.codemajin.net/wp-content/uploads/2024/12/CAMの原理.png)

## GradCAM （Gradient-weighted Class Activation Mapping）について

前述のように，CAMはGlobal Average Pooling（GAP）を用いた特定の構造を持つ畳み込みニューラルネットワーク（CNN）にのみ適用可能であるという制約がある．この制約を克服するために提案されたのがGrad-CAM（Gradient-weighted Class Activation Mapping）であり，全結合層の重み係数の代わりに勾配情報を用いることで，より一般的なCNNアーキテクチャに対しても可視化を可能としている．

Grad-CAMの動作は，以下のような手順で構成される．
1. 順伝播（Forward Pass）：入力画像をCNNに入力し，最終的な出力（たとえば，あるクラスに属する確率）を得る．
2. 逆伝播（Backward Pass）：最終出力に対して，関心のある特定クラス（例：「猫」）に関する損失関数の勾配を計算する．この勾配は，どの特徴マップ（畳み込み層の出力）がそのクラスに貢献したかを特定するために使用される．
3. 重み付け：各特徴マップに対して，その勾配の空間平均を計算し，これをその特徴マップに対応する重みとして用いる．この重みは，特徴マップが出力クラスに与えた寄与の度合いを示す．
4. ヒートマップの生成：得られた重み付き特徴マップを線形結合し，ReLU関数を適用することで，最終的なヒートマップを生成する．このヒートマップは，入力画像中で予測に最も寄与した領域を視覚的に示すものである．

![GradCam](https://www.codemajin.net/wp-content/uploads/2024/12/Grad-CAMの原理.png)

実装：[GradCam](https://github.com/jacobgil/pytorch-grad-cam)



# CLIP：テキストと画像を結ぶ

[Source](https://openai.com/ja-JP/index/clip/)

![](https://images.ctfassets.net/kftzwdyauwt9/fbc4f633-9ad4-4dc2-3809c22df5e0/0bd2d5abf90d052731538613e4a42668/overview-a.svg)

CLIP（Contrastive Language–Image Pre-training）は，ゼロショット転移，自然言語教師，およびマルチモーダル学習に関する多数の先行研究に基づいて構築された手法である．ゼロショット学習という概念自体は10年以上前に遡ることができ，未見の物体カテゴリーにも適用可能な汎用的手法として，長年にわたりコンピュータビジョン分野における主要な研究テーマであり続けてきた．

この分野における重要な洞察の一つは，自然言語を柔軟な予測空間として活用することで，モデルの汎用化能力と転移学習性能を向上させるという考え方である．例えば，2013年にStanford大学のRichard Socherらは，CIFAR-10データセットを用いて学習したモデルが単語ベクトルの埋め込み空間で予測を行い，未見の2つのクラスに対する予測が可能であることを示した．同年，このアプローチを拡張したDeVISE（Deep Visual-Semantic Embedding model）では，ImageNetで学習されたモデルをファインチューニングすることにより，学習に用いられていない物体クラスに対しても正しい予測を行う能力（汎化性能）を持つことが示された．

CLIPの設計に大きな影響を与えた研究として，2016年にFAIR（Facebook AI Research）のAng Liらが発表した研究が挙げられる．この研究では，自然言語を教師信号とし，従来の画像分類用データセット（例：ImageNet）に対するゼロショット転移を実現可能であることが示された．具体的には，Flickrに投稿された約3000万枚の画像に付随するタイトルや説明，タグなどの自然言語情報を活用することで，ImageNetのCNNモデルをファインチューニングし，より広範な視覚概念（visual n-grams）を予測可能とした．この手法はImageNetにおいて，ゼロショットで11.5％の精度を達成している．

近年発表されたCLIPは，このような自然言語教師に基づく視覚表現学習の研究潮流の中で，その成果を集約・発展させたものである．CLIPと同様の方向性を持つ研究には，Transformerアーキテクチャを採用したVirTex（自己回帰言語モデリング），ICMLM［34］（マスク言語モデリング），およびCLIPと同様の対照目的を医療画像分野に適用したConVIRTなどがある．これらの一連の研究は，自然言語と視覚情報を統合的に学習する枠組みの有効性を示しており，マルチモーダル学習の今後の展開において重要な指針となっている．

![CLIP](https://images.ctfassets.net/kftzwdyauwt9/5e490f66-703f-4228-221ca64049ed/8ed4358ba4b9f8779e07a2b15d7256e1/image_125.png?w=3840&q=90&fm=webp)
