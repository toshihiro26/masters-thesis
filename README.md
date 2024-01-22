# masters-thesis

## 途中経過
修士論文に一切手を付けていません．

## やったこと
- 動作生成における動作と対話の関係のまとめ
- 動作生成の手法の整頓
- 動作判断のLLMへの入力

### 動作生成における動作と対話の関係のまとめ
texfile/formulation.tex(https://github.com/toshihiro26/masters-thesis/blob/main/texfile/formulation.tex)

**※追加に至った理由を追記**

### 動作生成の手法の整頓
texfile/concept.tex(https://github.com/toshihiro26/masters-thesis/blob/main/texfile/concept.tex)

**※患者動作の生成手法の一連の流れを記述（ただし，細かく記述していく必要あり）**

### 動作判断のLLMへの入力
**より良い方法を見つけたい**

とにかく一度判断をLLMにやらせてみた．  

注意  
- 突貫で作ったのでかなりハードコーディング
- 主訴に関しては，無理やり結合した

#### 現状のプロンプト
~~~
system:
# 役割
あなたの仕事は対話ログに動作をつけるべきか判断することです．
# 出力形式
出力は次の形式のJSON文字列で返してください。
{
"判別結果1":{"対象の対話ログ": string, "判断理由": string},
...
}
~~~
~~~
user:
# 作成内容
与えられた対話ログにおけるP:から始まる対話ログのうち，動作をつけるべき対話ログとその判断理由を出力してください．

動作をつけるべきかどうかの判断基準として以下の項目を満たしてください．
- 主張したいことが発話者のことであること
- 主訴に関連している単語が出現していること 
~~~
~~~
assistant:
D: 山本葵さん、どうぞこちらへ
P: はい。
D: こんにちは。今日担当させていただきます学生の大西浩一と言います。よろしくお願いします。
P: お願いします。
D: 確認のため、お名前フルネームでお伺いしてもいいですか？　
P: 山本葵です。
D: 山本葵さんですね。間違いありません。それでは診察を始めさせていただきます。今日はどうされましたか？
P: 今日は咳と痰が続いていてきました。
D: 咳と痰が続いていらっしゃるんですね。それはいつ頃からですか？
P: 3週間前です。
D: 3週間前からなんですね。何かきっかけになることとかありましたか？
P: いや、きっかけになることは、うーん、そうですね、まあ風邪をひいてから続いてます。
D: 風邪をひいてから続いてるんですね。３週間前に風邪をひいて、そこからずっと続いてるってことですね。いいですか。
P: はい
D: ありがとうございます。わかりました。その他症状とかってありますか？
P: そうですね。１週間前ぐらいから学校の階段を登るとちょっと苦しいです。
D: わかりました。１週間前から学校の階段を登ると少し苦しいんですね。
P: はい
D: わかりました。ありがとうございます。熱は出ていらっしゃいますか？
P: 熱は出てないです。
D: わかりました。ありがとうございます。それでは、症状に関連するかもしれないので、ちょっと一般的なことをお聞かせください。
P: はい
D: ご家族で何かご病気された方っていらっしゃいますか？
P: 父が大腸がんで他界して、母が子宮がんで他界しました。あと兄も昨年心筋梗塞のために52歳で亡くなりました。
D: なるほど。わかりました。あと、そうですね、今の飲まれてるお薬とかってありますか？
P: えっと、２週間前ぐらいで医者にかかって、そのときに風邪薬を出されて、飲んでましたけど、まあよくならなかったので、昨日もう1回行って胸のレントゲンを取ったんですけど、それでもはっきりしなかったのでここに来ました。
D: わかりました。薬が効かなかったんですね。
P: そうですね。あと、２年前に高血圧指摘されたときから、降圧剤も飲んでます。
D: わかりました。降圧剤も飲まれてるんですね。えっと、お酒とかって飲まれますか？
P: 最近は休日にビール小瓶程度飲みますね。
D: はい。おタバコはお吸いになられますか？
P: 40歳まで20年間1日10本吸ってました。
D: わかりました。ありがとうございます。ちょっと聞きづらいんですけど、診察のためなんですけど、ちょっとお聞きしたいんですけど、あの月経は順調ですか？
P: はい、あ、間隔が開いてきて量も少なくなってきましたね。
D: ありがとうございます。わかりました。えっと、この症状に関して、自分の考えとかってありますか？
P: そうですね。まあ、風邪と思ってたんですけど、こんなに咳が治らなくて苦しいので、肺がんとか悪い病気じゃないかなってちょっと心配してます。
D: わかりました。ありがとうございます。今まで言ったことをまとめさせていただきたいんですけど、あの３週間前に風邪ひいてから、咳が続いていらっしゃると。
P: はい。
D: で、薬飲んでも効かなくて、えっと不安になって、こちらに来られたんですね。
P: うん
D: わかりました。ありがとうございます。えっと他に咳以外の症状は特にないってことでいいですか？
P: そうですね。まあ息苦しさはありますね。
D: はい、わかりました。ありがとうございます。それでは、診察に移りますので、またお呼びしますので、そちらでお待ちください。
P: はい
D: お願いします。ありがとうございます。失礼します。

~~~

結果
例(主訴: 咳と痰が続く)
~~~
{
"判別結果1": {
    "対象の対話ログ": "P: 今日は咳と痰が続いていてきました。",
    "判断理由": "発話者が自身の主訴について述べていて、主訴に関連する単語「咳」と「痰」が出現しているため"
},
"判別結果2": {
    "対象の対話ログ": "P: ３週間前からなんですね。何かきっかけになることとかありましたか？",
    "判断理由": "発話者の返答は質問への返答であり、主訴に直接的に関連する単語や発言者の主張が含まれていないため"
},
"判別結果3": {
    "対象の対話ログ": "P: いや、きっかけになることは、うーん、そうですね、まあ風邪をひいてから続いてます。",
    "判断理由": "発話者が主訴の発生について自身の症状に関連した情報「風邪」を提供しているため"
},
"判別結果4": {
    "対象の対話ログ": "P: そうですね。１週間前ぐらいから学校の階段を登るとちょっと苦しいです。",
    "判断理由": "この発言は新たな症状に関するものであるが、呼吸に関わる事項であるため、主訴に間接的に関連している可能性がある"
},
"判別結果5": {
    "対象の対話ログ": "P: 熱は出てないです。",
    "判断理由": "発話者は自身の状態について述べているが、これは主訴に直接関連する単語を含む主張ではない"
},
"判別結果6": {
    "対象の対話ログ": "P: 父が大腸がんで他界して、母が子宮がんで他界しました。あと兄も昨年心筋梗塞のために52歳で亡くなりました。",
    "判断理由": "発話者は自身の家族歴について述べているが、主訴に直接関係する内容ではない"
},
"判別結果7": {
    "対象の対話ログ": "P: えっと、２週間前ぐらいで医者にかかって、そのときに風邪薬を出されて、飲んでましたけど、まあよくならなかったので、昨日もう1回行って胸のレントゲンを取ったんですけど、それでもはっきりしなかったのでここに来ました。",
    "判断理由": "発話者が自身の治療経緯について述べており、「風邪薬」という主訴に関連する単語を使用しているため"
},
"判別結果8": {
    "対象の対話ログ": "P: そうですね。あと、２年前に高血圧指摘されたときから、降圧剤も飲んでます。",
    "判断理由": "発話者は自身が降圧剤を飲んでいると述べるが、これは主訴とは直接的な関連は見受けられない"
},
"判別結果9": {
    "対象の対話ログ": "P: 息苦しさはありますね。",
    "判断理由": "発話者の主張が自身の症状に対するものであること、また「息苦しさ」という単語が呼吸に関連することから、主訴に関連している可能性がある"
}
}
~~~

もう少し詳しく見る必要がある．

