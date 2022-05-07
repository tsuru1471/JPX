# JPX

# 目的、疑問等


最終目的!!!

> 対象範囲(22/7/5 ～ 22/10/7 ?)のすべてのcloseを予測し、rank付け

> →　その順位に基づいて実際のcloseから得られるdaily_spread_returnの値を最大化するのが目的


疑問!!!!!!!!

1. sumple_predictionとは？

> iter_testの返り値はおそらく example_test_files の６つのデータフレーム

> → したがって sumple_predictionとはsample_subissionのことだろうが、現在 21/12/06 と21/12/07のみ。最終的にはここが対象範囲に一致するのか？

> → 5月上旬に更新があるそうなので要確認

2.  train_data は？

> train_fiels + supplemental_fiels のstock_price.csvの和集合？

> → こちらも更新がなくいまいちつかみきれない


とりあえずやること

1. tqdm

> 5/07にて便利なイテレータ処理ライブラリであることを理解 

2. most vode EDAを見てデータ特性をつかむ

3. base_model 深すぎる特徴量などはつくらず、basicな加工のみのlightgbm(NNアンサンブルもなし！！！

4-1. 過去コンペから時系列(できれば同じ題材)の処理の仕方勉強

> https://www.kaggle.com/competitions/jpx-tokyo-stock-exchange-prediction/discussion/317038

4-2. kaggle本も見直してbasicなエンジニアリングをやりまくる

===============================================================================

中盤

5. いい具合でこのコンペに合いそうなエンジニアリングをdisuccusionを参考にしながら試す




# 5/04

JPXコンペ参加決定

まずはこのdisuccusionで概要をつかむ

> https://www.kaggle.com/code/chumajin/easy-to-understand-the-competition?scriptVersionId=94143164


# 5/06

データ等説明

![image](https://user-images.githubusercontent.com/92427575/167075783-efb5bf64-0dc7-4616-91d1-50757c26deec.png)



>train_files/trades
>
>trades_specによると StartDateおよびEndDateは基本的に木曜日に記入されている。またその内容はその前週の取引開始日および終了日
>
> → 前週の集計結果らしい(下訳)
>


![image](https://user-images.githubusercontent.com/92427575/167085889-360b722e-6e89-44e3-b2f5-245eac23a745.png)



# 5/07












# 株価基本知識

・そもそもどうして株価は変動する？？

> ![image](https://user-images.githubusercontent.com/92427575/167252592-222c790a-96ab-4df3-b973-dc0e64d96208.png)



・キャピタルゲイン : 株価の値上がりによって得られる売買差益。

↕

・インカムゲイン : 配当金など

・空売り : 下落を狙った投資。購入時よりも低い株価の時に買い戻して返すことで利益が生じる仕組み。

・出来高 = Volume : 取引された数。その人気を示し、株価と連動するらしい。ここでは日単位であることに注意

> Volumeが増加すれば上昇、人気が停滞すると買い手が減るために下落　と考えるそう

> ![image](https://user-images.githubusercontent.com/92427575/167252673-be91a1df-d67d-4def-b88f-5f87d2b4aabf.png)


・時価総額

> 上場株式　* 株価　で算出される値。企業の評価、価値を反映する。JPX/TOPIXではこれに浮動株(売買可能な市場に流通している株)比率を乗じる。





