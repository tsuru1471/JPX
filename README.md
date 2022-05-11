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

・secondary_stock_pries

> 流動性が低く、対象2000株に含まれていないものの Tokyo marketにて取引されている銘柄のデータ


・stock / securities 

>広義的にはsecuritiesが株式や債券などを包含する概念っぽいが、このコンペでどのように使い分けられているか不明。


・AdjustmentFactor 

>stock_prices のcolumn 。0.1～20の値をとるため、浮動株比率ではなさそう。
>
>　Used to calculate theoretical price/volume when split/reverse-split happens (NOT including dividend/allotment of shares).
> 
> 分割・併合時の理論株価・出来高の算出に使用（配当・増資は含まない）。
>
> → ここから、分割時(例えば、1株→2株 にした際) に理論的には価値が減少(0.5倍) になったことを反映させるための係数なのだろう。

# 5/08

・expected divident(配当金)

>![image](https://user-images.githubusercontent.com/92427575/167304823-8e5b4684-88b8-46b8-ad50-2eb6fa63f71f.png)
>
> ほぼnull。値があるなかでは0が多かった。最大値は1070
>
> アイデア) 普通に考えたら、null or 0 のやつが人気低いのでは？ こことcloseの相関調べよう
>
> 補足) 権利確定日を ex-dividend date , 権利落ち日を ex-light(s) date として定義してるっぽい


# 5/10

![image](https://user-images.githubusercontent.com/92427575/167515312-af18846a-abd2-476e-b142-fa2563af770f.png)

>　アイデア) やはり無配になる状況は業績低迷などが理由っぽい。ただし、新興企業など成長段階の企業は配当に充てるより、投資するために無配である場合が多い。
>>　
>> つまり増減が重要？無配そのものかどうかではなく、無配になってしまったかなど流れで見るのが良さそう。


・SupervisionFlag
>
> 例 code 6502
> 
>![image](https://user-images.githubusercontent.com/92427575/167520959-03c807ee-b188-41de-a444-87b42d3fbb1a.png)
>
> ずっと1(True)というわけでなく、廃止しなくなると0(False)に戻る。
> 
> アイデア) Trueになる、つまり監理銘柄に指定されると株価は急変する。TOB目的なら上昇し、経営難であるためならば下落する。ほかのデータからこのどちらかがわかればflagで予測できるはず！！



・2020-10-01

> ![image](https://user-images.githubusercontent.com/92427575/167619272-45098616-2d0c-4754-aab5-212b454c096d.png)
>
> 学習データ内での事件。1988株すべてのCloseがnanになっている。
>
> ![image](https://user-images.githubusercontent.com/92427575/167619766-e57161df-9b5f-4afa-afb1-c5f02194559a.png)

・ OHLCVグラフの描画
>
> plotly.graph_objects にて、操作可能なohlc曲線が描画可能
>
> as go とすれば go.Candlestick()にOHLCとdatesの引数を入力すればok
>
> defaultでは非営業日が飛んでしまうが死ぬほどデータあるし気にならんためpass
> 
> ![image](https://user-images.githubusercontent.com/92427575/167652742-90afdbef-e31c-442e-b0de-0717cf351d92.png)




# 5/11

・ taeget mean (同じ銘柄における全学習範囲に含まれるtargetの平均値、の分布)は非正規分布(right-skewed,big kutosis distribution)

> ![Uploading image.png…]()
>
>![image](https://user-images.githubusercontent.com/92427575/167820011-46735d16-f573-4a98-9f09-46a9ea795a73.png)
>
> kurtosis: 尖度/分布のとがりを表す値。
>
> skewnesss : 歪度 / 分布の歪みを表す値。right/leftは平均値が最頻値のどちら側にあるかを示している。
>
> →右に裾が伸びるright分布をPositively,左に裾が伸びるleft分布をnegatively-skewed-destributionとも呼ぶ。



# 株価基本知識

・そもそもどうして株価は変動する？？

> ![image](https://user-images.githubusercontent.com/92427575/167252592-222c790a-96ab-4df3-b973-dc0e64d96208.png)
>
> オークション方式で、証券取引所が売買を成立させる。そのため人気の株は高値でも買う人間が現れるために株価が上昇することになり、逆に低迷すると安い値段でしか買われなくなってしまうために下落する。


・ポートフォリオ : どの商品(株式)をどのくらい保有しているかの組み合わせのこと。

・キャピタルゲイン : 株価の値上がりによって得られる売買差益。

↕

・インカムゲイン : 配当金など

・空売り : 下落を狙った投資。購入時よりも低い株価の時に買い戻して返すことで利益が生じる仕組み。

・出来高 = Volume : 取引された数。その人気を示し、株価と連動するらしい。ここでは日単位であることに注意

> Volumeが増加すれば上昇、人気が停滞すると買い手が減るために下落　と考えるそう

> ![image](https://user-images.githubusercontent.com/92427575/167252673-be91a1df-d67d-4def-b88f-5f87d2b4aabf.png)


・時価総額

> 上場株式　* 株価　で算出される値。企業の評価、価値を反映する。JPX/TOPIXではこれに浮動株(売買可能な市場に流通している株)比率を乗
じる。

> https://www.nli-research.co.jp/files/topics/35583_ext_18_0.pdf?site=nli

> 出来高同様、流動性が大切なので、いくら発行株式が多くともそのほとんどが固定株で占められていた場合時価総額をそのまま信頼することができなくなってしまう。そのために浮動株比率で調整を行ってるっぽい。


・株式の分割/併合 split/reverse-split

> 1株を2株にするなど、既存の株を細分化することを「分割」と呼ぶ。逆に2株を1株にすれば「併合」である。
>
> 分割の実例はたくさんあったが、併合は見つからずその意図がつかめないままである。reverse-split と名付けられていることからも分割がよく起こりうるのだろう。


・配当(divident)を受け取るまでの流れ

> 権利付最終日(権利確定日2営業日前)  →  権利落ち日(権利確定日1営業日前)  → 権利確定日/決算日
>
> その期の配当を受け取るためには権利付最終日までに株式を購入し、株主名簿に登録される必要がある。


・EPS : Earnings Per Share
>
> 一株当たり利益 。 当期純利益/発行株式数 にて算出

・ PER : Price Earnings Ratio
>
>  株価 = PER * EPS 
>
> つまりPERは純利益に対しての株価を表す。低いほど割安となる。

・配当利回り
>
> 株価当たりの年間配当金額


・各利益
>
>![image](https://user-images.githubusercontent.com/92427575/167518568-842c769e-499a-4fb6-a622-fa3f0b07cd73.png)


・監理銘柄
>
> 上場廃止の恐れがあると証券取引所が指定した銘柄のこと。廃止の恐れがなくなると解除される。また、実際に廃止となった場合には整理銘柄となる。

・TOB : Take Over Bid
>
> M&A 目的で市場外でその銘柄を投資家から買いつけること。当然成功させるために割高で買い付けるのだが、その影響で被TOB銘柄の市場価格も上昇することになる。
>
> また、TOB側銘柄の市場価格も変動する。TOBによって成長が見込まれれば上昇など。


・市場分布 (22/04より)
>![image](https://user-images.githubusercontent.com/92427575/167531918-bfcd8f15-0e4a-41cb-a933-4b3563200c58.png)


・OHLC(V)
>
> Open,High,Low,Close,(Volume)の総称、ローソク足チャート → candlestick とも呼ぶ
>
>![image](https://user-images.githubusercontent.com/92427575/167624090-ce687822-9096-40c1-a99e-b5dd982d7f4c.png)


