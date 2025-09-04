<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "76438ce4e5d48982d48f1b55c981caac",
  "translation_date": "2025-09-04T00:01:30+00:00",
  "source_file": "4-Classification/1-Introduction/README.md",
  "language_code": "ja"
}
-->
# 分類の紹介

この4つのレッスンでは、古典的な機械学習の基本的な焦点である_分類_について探求します。アジアとインドの素晴らしい料理に関するデータセットを使い、さまざまな分類アルゴリズムを実践していきます。お腹が空いてきましたか？

![ほんのひとつまみ！](../../../../translated_images/pinch.1b035ec9ba7e0d408313b551b60c721c9c290b2dd2094115bc87e6ddacd114c9.ja.png)

> このレッスンでパンアジア料理を祝おう！画像提供: [Jen Looper](https://twitter.com/jenlooper)

分類は[教師あり学習](https://wikipedia.org/wiki/Supervised_learning)の一形態で、回帰手法と多くの共通点を持っています。機械学習がデータセットを使って値や名前を予測することだとすれば、分類は一般的に2つのグループに分けられます：_二値分類_と_多クラス分類_です。

[![分類の紹介](https://img.youtube.com/vi/eg8DJYwdMyg/0.jpg)](https://youtu.be/eg8DJYwdMyg "分類の紹介")

> 🎥 上の画像をクリックして動画を視聴: MITのJohn Guttagが分類を紹介

覚えておきましょう：

- **線形回帰**では、変数間の関係を予測し、新しいデータポイントがその線に対してどこに位置するかを正確に予測するのに役立ちました。例えば、_9月と12月の間でカボチャの価格がどうなるか_を予測できました。
- **ロジスティック回帰**では、「二値カテゴリ」を発見するのに役立ちました。例えば、この価格帯で_このカボチャはオレンジ色か、それともオレンジ色ではないか_を判断しました。

分類では、さまざまなアルゴリズムを使用して、データポイントのラベルやクラスを決定する方法を見つけます。この料理データを使って、材料のグループを観察することで、その料理の起源を特定できるかどうかを見てみましょう。

## [講義前のクイズ](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/19/)

> ### [このレッスンはRでも利用可能です！](../../../../4-Classification/1-Introduction/solution/R/lesson_10.html)

### はじめに

分類は、機械学習研究者やデータサイエンティストにとって基本的な活動の1つです。例えば、二値値の基本的な分類（「このメールはスパムかどうか？」）から、コンピュータビジョンを使用した複雑な画像分類やセグメンテーションまで、データをクラスに分類し、それに関する質問をする能力は常に役立ちます。

科学的に言うと、分類手法は予測モデルを作成し、入力変数と出力変数の関係をマッピングできるようにします。

![二値分類 vs. 多クラス分類](../../../../translated_images/binary-multiclass.b56d0c86c81105a697dddd82242c1d11e4d78b7afefea07a44627a0f1111c1a9.ja.png)

> 分類アルゴリズムが扱う二値問題と多クラス問題。インフォグラフィック提供: [Jen Looper](https://twitter.com/jenlooper)

データをクリーニングし、視覚化し、機械学習タスクの準備を始める前に、機械学習を使用してデータを分類するさまざまな方法について少し学びましょう。

[統計学](https://wikipedia.org/wiki/Statistical_classification)に由来する古典的な機械学習を使用した分類では、`smoker`、`weight`、`age`などの特徴を使用して、_X病を発症する可能性_を判断します。以前に行った回帰演習と同様に、教師あり学習技術として、データにはラベルが付けられており、機械学習アルゴリズムはそのラベルを使用してデータセットのクラス（または「特徴」）を分類し、グループや結果に割り当てます。

✅ 料理に関するデータセットを想像してみてください。多クラスモデルではどのような質問に答えられるでしょうか？二値モデルではどのような質問に答えられるでしょうか？例えば、ある料理がフェヌグリークを使用する可能性があるかどうかを判断したい場合はどうでしょう？また、スターアニス、アーティチョーク、カリフラワー、ホースラディッシュが詰まった買い物袋を渡されたとき、それで典型的なインド料理を作れるかどうかを知りたい場合はどうでしょう？

[![クレイジーなミステリーバスケット](https://img.youtube.com/vi/GuTeDbaNoEU/0.jpg)](https://youtu.be/GuTeDbaNoEU "クレイジーなミステリーバスケット")

> 🎥 上の画像をクリックして動画を視聴。料理番組『Chopped』の全体のコンセプトは「ミステリーバスケット」で、シェフたちがランダムな材料から料理を作らなければなりません。きっと機械学習モデルが役立つでしょう！

## こんにちは「分類器」

この料理データセットに関して私たちが尋ねたい質問は、実際には**多クラスの質問**です。なぜなら、扱うべき国別の料理がいくつもあるからです。材料の一群が与えられた場合、それらの多くのクラスのうちどれにデータが適合するでしょうか？

Scikit-learnは、解決したい問題の種類に応じて、データを分類するためのさまざまなアルゴリズムを提供しています。次の2つのレッスンでは、これらのアルゴリズムのいくつかについて学びます。

## 演習 - データをクリーニングしてバランスを取る

このプロジェクトを開始する前の最初のタスクは、データをクリーニングし、**バランスを取る**ことで、より良い結果を得ることです。このフォルダのルートにある空の_notebook.ipynb_ファイルから始めてください。

最初にインストールするのは[imblearn](https://imbalanced-learn.org/stable/)です。これは、データのバランスをより良くするためのScikit-learnパッケージです（このタスクについては後で詳しく学びます）。

1. `imblearn`をインストールするには、以下のように`pip install`を実行します：

    ```python
    pip install imblearn
    ```

1. 必要なパッケージをインポートしてデータをインポートし、視覚化します。また、`imblearn`から`SMOTE`をインポートします。

    ```python
    import pandas as pd
    import matplotlib.pyplot as plt
    import matplotlib as mpl
    import numpy as np
    from imblearn.over_sampling import SMOTE
    ```

    これで、次にデータをインポートする準備が整いました。

1. 次のタスクはデータをインポートすることです：

    ```python
    df  = pd.read_csv('../data/cuisines.csv')
    ```

   `read_csv()`を使用すると、_cusines.csv_ファイルの内容を読み取り、変数`df`に配置します。

1. データの形状を確認します：

    ```python
    df.head()
    ```

   最初の5行は次のように見えます：

    ```output
    |     | Unnamed: 0 | cuisine | almond | angelica | anise | anise_seed | apple | apple_brandy | apricot | armagnac | ... | whiskey | white_bread | white_wine | whole_grain_wheat_flour | wine | wood | yam | yeast | yogurt | zucchini |
    | --- | ---------- | ------- | ------ | -------- | ----- | ---------- | ----- | ------------ | ------- | -------- | --- | ------- | ----------- | ---------- | ----------------------- | ---- | ---- | --- | ----- | ------ | -------- |
    | 0   | 65         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
    | 1   | 66         | indian  | 1      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
    | 2   | 67         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
    | 3   | 68         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 0      | 0        |
    | 4   | 69         | indian  | 0      | 0        | 0     | 0          | 0     | 0            | 0       | 0        | ... | 0       | 0           | 0          | 0                       | 0    | 0    | 0   | 0     | 1      | 0        |
    ```

1. このデータについての情報を`info()`を呼び出して取得します：

    ```python
    df.info()
    ```

    出力は次のようになります：

    ```output
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 2448 entries, 0 to 2447
    Columns: 385 entries, Unnamed: 0 to zucchini
    dtypes: int64(384), object(1)
    memory usage: 7.2+ MB
    ```

## 演習 - 料理について学ぶ

ここから作業がより興味深くなります。料理ごとのデータの分布を調べてみましょう。

1. `barh()`を呼び出してデータを棒グラフとしてプロットします：

    ```python
    df.cuisine.value_counts().plot.barh()
    ```

    ![料理データの分布](../../../../translated_images/cuisine-dist.d0cc2d551abe5c25f83d73a5f560927e4a061e9a4560bac1e97d35682ef3ca6d.ja.png)

    料理の数は有限ですが、データの分布は不均一です。これを修正することができます！その前に、もう少し探索してみましょう。

1. 料理ごとに利用可能なデータ量を調べて印刷します：

    ```python
    thai_df = df[(df.cuisine == "thai")]
    japanese_df = df[(df.cuisine == "japanese")]
    chinese_df = df[(df.cuisine == "chinese")]
    indian_df = df[(df.cuisine == "indian")]
    korean_df = df[(df.cuisine == "korean")]
    
    print(f'thai df: {thai_df.shape}')
    print(f'japanese df: {japanese_df.shape}')
    print(f'chinese df: {chinese_df.shape}')
    print(f'indian df: {indian_df.shape}')
    print(f'korean df: {korean_df.shape}')
    ```

    出力は次のようになります：

    ```output
    thai df: (289, 385)
    japanese df: (320, 385)
    chinese df: (442, 385)
    indian df: (598, 385)
    korean df: (799, 385)
    ```

## 材料を発見する

次に、データをさらに掘り下げて、料理ごとの典型的な材料を学びます。異なる料理間で混乱を引き起こす再発データをクリーニングする必要があります。この問題について学びましょう。

1. Pythonで`create_ingredient()`関数を作成して、材料データフレームを作成します。この関数は、役に立たない列を削除し、材料をそのカウントでソートすることから始めます：

    ```python
    def create_ingredient_df(df):
        ingredient_df = df.T.drop(['cuisine','Unnamed: 0']).sum(axis=1).to_frame('value')
        ingredient_df = ingredient_df[(ingredient_df.T != 0).any()]
        ingredient_df = ingredient_df.sort_values(by='value', ascending=False,
        inplace=False)
        return ingredient_df
    ```

   この関数を使用して、料理ごとのトップ10の人気材料を把握できます。

1. `create_ingredient()`を呼び出し、`barh()`を呼び出してプロットします：

    ```python
    thai_ingredient_df = create_ingredient_df(thai_df)
    thai_ingredient_df.head(10).plot.barh()
    ```

    ![タイ料理](../../../../translated_images/thai.0269dbab2e78bd38a132067759fe980008bdb80b6d778e5313448dbe12bed846.ja.png)

1. 日本料理のデータについても同じことを行います：

    ```python
    japanese_ingredient_df = create_ingredient_df(japanese_df)
    japanese_ingredient_df.head(10).plot.barh()
    ```

    ![日本料理](../../../../translated_images/japanese.30260486f2a05c463c8faa62ebe7b38f0961ed293bd9a6db8eef5d3f0cf17155.ja.png)

1. 中国料理の材料についても同様です：

    ```python
    chinese_ingredient_df = create_ingredient_df(chinese_df)
    chinese_ingredient_df.head(10).plot.barh()
    ```

    ![中国料理](../../../../translated_images/chinese.e62cafa5309f111afd1b54490336daf4e927ce32bed837069a0b7ce481dfae8d.ja.png)

1. インド料理の材料をプロットします：

    ```python
    indian_ingredient_df = create_ingredient_df(indian_df)
    indian_ingredient_df.head(10).plot.barh()
    ```

    ![インド料理](../../../../translated_images/indian.2c4292002af1a1f97a4a24fec6b1459ee8ff616c3822ae56bb62b9903e192af6.ja.png)

1. 最後に、韓国料理の材料をプロットします：

    ```python
    korean_ingredient_df = create_ingredient_df(korean_df)
    korean_ingredient_df.head(10).plot.barh()
    ```

    ![韓国料理](../../../../translated_images/korean.4a4f0274f3d9805a65e61f05597eeaad8620b03be23a2c0a705c023f65fad2c0.ja.png)

1. 次に、異なる料理間で混乱を引き起こす最も一般的な材料を`drop()`を呼び出して削除します：

   みんなが大好きな米、ニンニク、生姜を削除します！

    ```python
    feature_df= df.drop(['cuisine','Unnamed: 0','rice','garlic','ginger'], axis=1)
    labels_df = df.cuisine #.unique()
    feature_df.head()
    ```

## データセットのバランスを取る

データをクリーニングしたら、[SMOTE](https://imbalanced-learn.org/dev/references/generated/imblearn.over_sampling.SMOTE.html)（Synthetic Minority Over-sampling Technique）を使用してバランスを取ります。

1. `fit_resample()`を呼び出します。この戦略は補間によって新しいサンプルを生成します。

    ```python
    oversample = SMOTE()
    transformed_feature_df, transformed_label_df = oversample.fit_resample(feature_df, labels_df)
    ```

    データをバランスさせることで、分類時により良い結果が得られます。二値分類を考えてみてください。データの大部分が1つのクラスである場合、機械学習モデルはそのクラスをより頻繁に予測する傾向があります。ただし、データが多いためです。データのバランスを取ることで、この不均衡を取り除くのに役立ちます。

1. 次に、材料ごとのラベル数を確認します：

    ```python
    print(f'new label count: {transformed_label_df.value_counts()}')
    print(f'old label count: {df.cuisine.value_counts()}')
    ```

    出力は次のようになります：

    ```output
    new label count: korean      799
    chinese     799
    indian      799
    japanese    799
    thai        799
    Name: cuisine, dtype: int64
    old label count: korean      799
    indian      598
    chinese     442
    japanese    320
    thai        289
    Name: cuisine, dtype: int64
    ```

    データはきれいでバランスが取れており、とても美味しそうです！

1. 最後のステップは、ラベルと特徴を含むバランスの取れたデータを新しいデータフレームに保存し、ファイルにエクスポートすることです：

    ```python
    transformed_df = pd.concat([transformed_label_df,transformed_feature_df],axis=1, join='outer')
    ```

1. `transformed_df.head()`と`transformed_df.info()`を使用してデータをもう一度確認できます。このデータのコピーを保存して、今後のレッスンで使用してください：

    ```python
    transformed_df.head()
    transformed_df.info()
    transformed_df.to_csv("../data/cleaned_cuisines.csv")
    ```

    この新しいCSVは、ルートデータフォルダに保存されます。

---

## 🚀チャレンジ

このカリキュラムには、いくつかの興味深いデータセットが含まれています。`data`フォルダを掘り下げて、二値または多クラス分類に適したデータセットが含まれているか確認してください。このデータセットにどのような質問をしますか？

## [講義後のクイズ](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/20/)

## 復習と自己学習

SMOTEのAPIを調べてみましょう。どのようなユースケースに最適で、どのような問題を解決するのでしょうか？

## 課題 

[分類手法を探求する](assignment.md)

---

**免責事項**:  
この文書は、AI翻訳サービス [Co-op Translator](https://github.com/Azure/co-op-translator) を使用して翻訳されています。正確性を追求しておりますが、自動翻訳には誤りや不正確な部分が含まれる可能性があることをご承知ください。元の言語で記載された文書が正式な情報源とみなされるべきです。重要な情報については、専門の人間による翻訳を推奨します。この翻訳の使用に起因する誤解や誤った解釈について、当方は一切の責任を負いません。