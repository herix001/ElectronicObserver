## 艦これの仕様に関する雑多なメモ
---

#### 特注家具職人が必要な条件  
2000≦price<20000  
とくに特別なフラグはない。  

#### ケッコンカッコカリ後の耐久値  
ケッコン前の最大耐久値及び「最大耐久値の最大値」にのみ依存する。艦種や艦型、ケッコンのタイミングなどの影響は受けない。
 
- HP < 30 : +4
- HP < 40 : +5
- HP < 50 : +6
- HP < 70 : +7
- HP < 90 : +8
- HP >= 90 : +9  

「最大耐久値の最大値」は艦ごとに個別に設定されており、この値を超えることはない。  
殆どの艦では上記増分値を足しても問題ないほど大きく設定されているが、Bismarck drei など一部の艦娘はこれによって上昇量が制限されている。

#### パラメータの算出式  
対潜・回避・索敵の3種パラメータは、以下の式で求められる。  

```
value = min + int( ( max - min ) * Lv / 99 )  
```

なお、max = Lv99時点での値。  
「改装して最大値は変わらないが最小値が上昇する艦娘」(島風の回避など)の場合、Lv100を超えると改装前のほうが強くなることがある。  

火力・雷装・対空・装甲はレベルアップ時にランダムに上昇する(らしい)。  
なお、改装時の4種パラメータは、  

```
value = min + ceil( ( max - min ) * ( 0.4 or 0.8 ) * Lv / 99 )  
```

で求められる(0.4 or 0.8 はランダム)。  

#### 季節の衣替え艦娘  
クリスマス・年末・正月に衣装替えした艦娘のデータは ID 901 以降に保存されており、図鑑からのみ参照できる。  
~~なお、システム内では 501 以降の艦は深海棲艦として扱われるため、一応深海棲艦扱い。~~ 現在は修正済。  

#### SuperAAItem  
以下のアイテムが該当する。

* 3: 10cm連装高角砲
* 9: 46cm三連装砲
* 10: 12.7cm連装高角砲
* 35: 三式弾
* 48: 12.7cm単装高角砲

現在は未使用?  

#### 敵艦載機グラフィック
* **1 (敵演習艦載機)**
    * IDにかかわらず、演習の艦載機は全てこれになる
* **2 (緑)**
    * 下記装備に該当しないもの
* **3 (オレンジ)**
    * 517: 深海棲艦攻 Mark.II
    * 520: 深海棲艦戦 Mark.II
    * 524: 深海棲艦爆 Mark.II
* **4 (青)**
    * 518: 深海棲艦攻 Mark.III
    * 521: 深海棲艦戦 Mark.III
    * 525: 深海棲艦偵察機
    * 526: 飛び魚偵察機
    * 544: 深海爆雷投射機 Mk.II *※522: 飛び魚艦戦の設定ミス？*
    * 546: 飛び魚艦爆
* **5 (たこ焼き)**
    * 547: 深海猫艦戦
* **6 (たこ焼き)**
    * 548: 深海地獄艦爆
* **7 (たこ焼き)**
    * 549: 深海復讐艦攻

#### ダメージ処理  
実際の状態にかかわらず、15未満だと常に非クリティカル, 40以上だと常にクリティカルとして表示される。  
ちなみに、「命中したが0ダメージ」という状況も存在する。  
実際に出せるかはさておき、表示上の最大値は9999。  
また、「かばう」が発動した場合は 0.1 がフラグとして加算される(もちろん実ダメージには影響しない)。  

#### ダメコン処理の優先度  
ダメコンの種類にかかわらず、スロットの上にあるほうから使われる。　　

#### 挟叉  
命中フラグは以下のようになっている。  

* `HITFLG_NOATTACKED` : 攻撃せず(-1)
* `HITFLG_MISS` : ミス(0)
* `HITFLG_NORMAL` : 命中(1)
* `HITFLG_CRITICAL` : クリティカル(2)
* `HITFLG_KYOUSA` : 狭叉(3) (原文ママ, 未実装?)  

#### 航空支援での敵機撃墜  
航空支援でも敵機を撃墜することは可能。  
ただし既に航空戦フェーズは終わっているので、狙ってまでする事ではなさそう。  

####レアリティの設定ミス  
羽黒改二はデータ上は金色扱い(グラフィック上は虹色)。  
武蔵(改)はデータ上は輝虹扱い(グラフィック上は桜虹、こちらは建造画面の背景が分かりやすい)  
他にもいた気がする…  
このレアリティデータは撃沈時のエフェクト表示に使用されるはずなので、撃沈させれば分かるが怖すぎてできない  

#### 撃沈・退避時のグラフィック  
R' = G' = B' = ( R + G + B ) / 3

#### ダメコン使用時の回復量
- 応急修理要員 : HP = int( HPMax * 0.2 )
    - 旗艦大破進撃時は HP = int( HPMax * 0.5 )
- 応急修理女神 : HP = HPMax ( +燃料・弾薬全回復 )  

#### 保有数による出撃時の制限  
- 艦船がドロップしない条件  
艦船空き == 0 or 装備空き <= 2  
- イベント海域出撃不可条件  
艦船空き < 5 or 装備空き < 20

#### 攻撃種別  
* 昼戦
    * 0: 砲撃  
    * 1: レーザー攻撃
    * 2: 連続射撃
    * 3: カットイン(主砲/副砲)  
    * 4: カットイン(主砲/電探)
    * 5: カットイン(主砲/徹甲弾)
    * 6: カットイン(主砲/主砲)
    * 7: 空撃
    * 8: 爆雷攻撃
    * 9: 雷撃
    * 10: ロケット砲撃

* 夜戦
    * 0: 砲撃  
    * 1: 連続射撃
    * 2: カットイン(主砲/魚雷)
    * 3: カットイン(魚雷/魚雷)
    * 4: カットイン(主砲/主砲/副砲)
    * 5: カットイン(主砲/主砲/主砲)
    * 6: ???
    * 7: 空撃
    * 8: 爆雷攻撃
    * 9: 雷撃  
    * 10: ロケット砲撃

なお、通常 7 以降のデータは送られてこないが、以下の式によって蔵側で判定される。  

```
//昼戦の攻撃種別判定
if ( 攻撃種別 != 0 ) (そのまま採用);
else if ( 防御側 == 陸上基地 && EquipmentType[2](装備カテゴリ) == 対地装備 の装備を装備している ) 攻撃種別 = 10(ロケット砲撃);
else if ( 攻撃側 == ( 軽空母 | 正規空母 | 装甲空母 ) ) 攻撃種別 = 7(空撃);
else if ( 防御側 == ( 潜水艦 | 潜水空母 ) ) {
	if ( 攻撃側 == ( 航空巡洋艦 | 航空戦艦 | 水上機母艦 | 揚陸艦 ) ) 攻撃種別 = 7(空撃);
	else 攻撃種別 = 8(爆雷攻撃);
}
else if ( 最初の表示装備(api_si_list)のカテゴリ == 魚雷 ) 攻撃種別 = 9(雷撃);
else 攻撃種別 = 0(砲撃);
```

```
//夜戦の攻撃種別判定
if ( 攻撃種別 != 0 ) (そのまま採用);
else if ( 防御側 == 陸上基地 && EquipmentType[2](装備カテゴリ) == 対地装備 の装備を装備している ) 攻撃種別 = 10(ロケット砲撃);
else if ( 攻撃側 == ( 軽空母 | 正規空母 | 装甲空母 ) ) 攻撃種別 = 7(空撃);
else if ( 防御側 == ( 潜水艦 | 潜水空母 ) ) {
	if ( 攻撃側 == ( 航空巡洋艦 | 航空戦艦 | 水上機母艦 | 揚陸艦 ) ) 攻撃種別 = 7(空撃);
	else 攻撃種別 = 8(爆雷攻撃);
} else if ( 攻撃側 == ( 潜水艦 | 潜水空母 ) ) 攻撃種別 = 9(雷撃);
else if ( 最初の表示装備(api_si_list)のカテゴリ == 魚雷 ) 攻撃種別 = 9(雷撃);
else 攻撃種別 = 0(砲撃);
```

なお、「最初の表示装備」は戦闘時にカットインとして表示される装備であり、第一スロットに装備されているアイテムとは異なる場合がある。

#### 対空カットイン  
* 1：高角砲x2/電探
* 2：高角砲/電探
* 3：高角砲x2
* 4：大口径主砲/三式弾/高射装置/電探
* 5：高角砲+高射装置x2/電探
* 6：大口径主砲/三式弾/高射装置
* 7：高角砲/高射装置/電探
* 8：高角砲+高射装置/電探
* 9：高角砲/高射装置

(個艦については)この優先度で判定される？なお、1-3は秋月(改)専用。

#### 敵ボイス  
`areaID` `mapNo` `index` `shipID`  

#### 装備種別  
* [0]: 大分類？
* [1]: 図鑑カテゴリ/戦闘分類(`getSlotItemCardType`)
* [2]: 装備種別(`getSlotItemEquipType`)
* [3]: アイコン(`getSlotItemIconType`)

詳細については apilist.txt を参照すること。

#### 夜戦魚雷連撃  
一応実装されているらしい。  
連撃フラグが立っており、かつ攻撃表示装備[0]が主砲/副砲( EquipmentType[1] == ( 1 | 2 ) )でないときに発動する。  
そういったデータが送られてくることはないので通常の手段では見ることはできない。  

#### 対空能力の優先度？  

ある部分で使われている処理。何かの助けになるかもしれない

```
int flag = 0;
foreach ( 装備スロット ) {
	int priority = 0;
	if ( カテゴリ == 対空強化弾 ) priority = 1;
	else if ( アイコン == 高角砲 ) priority = 3;
	else if ( 装備 == 12cm30連装噴進砲 ) priority = 2;
	else if ( カテゴリ == 対空機銃 ) priority = 4;
	else if ( アイコン == 電探 && 対空 > 0 ) priority = 4;

	if ( priority > 0 && ( priority < flag || flag == 0 ) )
		flag = priority;	//優先度の高いものを選択
}
if ( flag == 2 ) flag = 3;
else if ( flag == 3 ) flag = 2;		//謎のスワップ 
return flag;
```

#### 泊地修理の仕様
(英wikiその他より。間違いがあるかも)

* 判定はタイマーが20分以上になった状態で母港に移動すると行われる。詳細は後述。
* 工作艦を旗艦に置く必要がある。第1艦隊である必要はない。
* 以下の条件を満たすとき修理は行われない。
    * 旗艦が中破以上のダメージを受けている。(?)
    * 旗艦が入渠中である。
    * 艦隊が遠征中である。
* 以下の条件を満たす艦娘は修理対象とならない。
    * 中破以上のダメージを受けている。
    * 修理の範囲外にいる。
    * 既に入渠している。
    * 修復に必要な資源が足りない。
* 編成を変更した瞬間にタイマーはリセットされる。  
言い換えれば、編成を変更した時点でタイマーのカウントは開始される。
* 艦隊を出撃させてもタイマーはリセットされない。
* 随伴艦を入渠させること・装備変更することはタイマーに影響しない。
* 修理中はオフラインでも構わない。
* 修理はコンディション(cond)に依存しない。
* 修理可能艦数は ( 2 + 艦艇修理施設の装備数 )。旗艦も含む。
* 20分以上経過した状態で中断すると少なくとも1回復する。そのため、高レベル・長時間入渠の艦は入渠での修理よりも早く終わることがある。
* 修理コストは*中断しない限り*入渠での修理と同じになる。  
中断した場合は入渠より多くかかることがある。
* 修理時間は分単位切り捨てで処理される。
* HP1当たりの回復時間は入渠と同じ。  
`回復量 = max( ceiling( 修理時間 / 各艦の回復単位時間 ) - 1, 1 )` となる。
* 消費資材は回復値に応じる(ceil)。消費資材が足りない場合、修理は行われない。  
但し、タイマー開始時に足りなくても修理処理時に自然回復によって資源が賄えた場合は修理が行われる(自然回復判定のほうが先)。
* 修理時間のタイマーは共通になっているらしく、複数艦隊で修理するときは注意が必要?

以下のサイトに詳しい:

* [複数艦隊の泊地修理の仕様](http://fujieda.blog.jp/archives/20645383.html)
* [明石の泊地修理の仕様 - 計時について](http://fujieda.blog.jp/archives/12988568.html)
* [明石の泊地修理の仕様 - 耐久の回復量について](http://fujieda.blog.jp/archives/13023308.html)

#### 時報の読み込みタイミング  
サーバの混雑を回避するためか、時間の5分ほど前から読み込みを開始することがある。  
詳細は未調査。  

#### 戦闘勝利判定  
確証はないが(特に撃沈艦ありのとき)、観測儀では以下のように実装している。

```
味方損害率 = int( ( 味方開始時HP - 味方終了時HP ) / 味方開始時HP * 100 );
敵損害率 = int( ( 敵開始時HP - 敵終了時HP ) / 敵開始時HP * 100 );

if ( 敵損害率 >= 100 ) {
    if ( 味方損害率 <= 0 ) { //ダメコン発動時に負になる可能性がある
        rank = SS;
    } else {
        rank = S;
    }
} else if ( 敵撃沈数 >= round( 敵艦総数 * 0.6 ) ) {
    rank = A;
} else if ( 敵旗艦撃沈 || 敵損害率 > 味方損害率 * 2.5 ) {
    rank = B;
} else if ( 敵損害率 > 味方損害率 || 敵損害率 >= 50 ) {
    rank = C;
} else {
    rank = D;
}

if ( 味方に撃沈艦がある ) {
    rank = min( rank, A ) - 1;
}
```

なお、連合艦隊での判定は少なくとも「本隊だけ」ではない模様。調査中  
ver. 0.2.1 現在、観測儀では本体・随伴隊全員の損害率を計算している。  

損害率が自軍71.5%/敵軍67.6%(旗艦生存/撃沈3隻)、自軍62.2%/敵軍57.8%(旗艦生存/撃沈3隻)でC敗北となったことがある。  
敵側の損害率が一定以上だとC敗北になる？  
C敗北判定の 敵損害率 >= 50 は試験実装。  
これまでの例外判定メモ：  

* 自軍71.5%/敵軍67.6%(旗艦生存/撃沈3隻); 判定D→実際C
* 自軍62.2%/敵軍57.8%(旗艦生存/撃沈3隻); 判定D→実際C
* 自軍32.5%/敵軍32.7%(旗艦生存/撃沈2隻); 判定D→実際C

#### 一度に大破できる艦数  
表示上は3隻?  

#### 連合艦隊における電探の渦潮被害軽減効果  
艦隊ごとに判定されるため、どちらの艦隊にも載せる必要がある。

#### '15 冬イベントの海域ゲージHP  
全て甲作戦のもの。

* E-1 : 192 (旗艦HP45; 最低5回撃破)
* E-2 : 720 (旗艦HP通常96/最終160 160以下で最終形態 最低7回撃破)
* E-3 : 1050 (旗艦HP270; 最低4回撃破)
* E-4 : 1925 (旗艦HP350; 最低6回撃破)
* E-5 : 3000 (旗艦HP500; 最低6回撃破)

#### 海域HPゲージ  
わずかでもダメージを与えないと正常な値にならない。  
(ex. 初期は300/300でも、30ダメージ与えると2370/2400になる、など)  
と思ったが、最大HPが1000以上だと正しく取得できる？  
'15冬イベ以前は初期値300、'15冬イベは9999  

#### 猫った時のAPIレスポンス  
練習巡洋艦旗艦で開発すると落ちる不具合を利用したもの。(2015/02/08)  

* `api_result` : 100  
* `api_result_msg` : "申し訳ありませんがブラウザを再起動し再ログインしてください。"

#### 特殊な装備判定  
Bismarck dreiに対する魚雷装備・試製51cm連装砲の装備判定は、改装UIシステムの中で*例外的に決め打ち*されている。  
具体的には、

* Bismarck drei : 魚雷
* 大和(改), 武蔵(改), 長門改, 陸奥改 : 大口径主砲(II)

が装備可能になるようになっている。  
(艦船IDが装備可能リストに含まれていた場合は*マスターデータの内容にかかわらず*装備可能にする、というコードになっている)  
つまりある意味では不正な装備ともいえる(?)  
~~そのために新艦種を追加するのも面倒なのは分かるが、ちょっとこれはひどい気がする~~  

#### 試製51cm連装砲の怪  

`api_start2`のデータでは、装備種別が[1,1,3,3]=[砲,主砲,大口径主砲,大口径主砲]となっており、なぜか大口径主砲(II)ではない。  
装備画面ではきちんと扱われているところを見ると、蔵のどこかで書き換えているらしいが…？  
2015/02/16現在、図鑑において「装備種別」「装備可能艦種」が表示されていないことから、マスターを書き換えるコードがどこかに存在するはず、である。  
2015/02/25現在、commonAssets内に`SLOTITEM_SPECIAL_FLAGS`なるオブジェクトが存在し、ID128(=試製51cm連装砲)のeqTypeを38(=大口径主砲(II))にするらしき指定がなされているのを確認。  

#### 主要な任務一覧  

|任務ID|任務名|達成条件|追加条件|
|--:|:--|:--|:--|
|201|敵艦隊を撃破せよ！|勝利1
|216|敵艦隊主力を撃滅せよ！|戦闘1
|210|敵艦隊を10回邀撃せよ！|戦闘10
|211|敵空母を3隻撃沈せよ！|空母3
|212|敵輸送船団を叩け！|輸送5
|218|敵補給艦を3隻撃沈せよ！|輸送3
|226|南西諸島海域の制海権を握れ！|2-(1~5)ボス勝利5
|230|敵潜水艦を制圧せよ！|潜水6
|213|海上通商破壊作戦|輸送20
|214|あ号作戦|出撃36/S勝利6/ボス24/ボス勝利12
|220|い号作戦|空母20
|221|ろ号作戦|輸送50
|228|海上護衛戦|潜水15
|229|敵東方艦隊を撃滅せよ！|4-(1~4)ボス勝利12
|242|敵東方中枢艦隊を撃破せよ！|4-4ボス勝利1
|243|南方海域珊瑚諸島沖の制空権を握れ！|5-2ボスS勝利2
|261|海上輸送路の安全確保に努めよ！|1-5ボスA勝利3
|241|敵北方艦隊主力を撃滅せよ！|3-(3~5)ボス勝利5
|256|「潜水艦隊」出撃せよ！|6-1ボスS勝利3
|257|「水雷戦隊」南西へ！|1-4ボスS勝利1|要軽巡旗艦、軽巡3隻まで、他駆逐艦　他艦種禁止
|249|「第五戦隊」出撃せよ！|2-5ボスS勝利1|要「那智」「妙高」「羽黒」
|259|「水上打撃部隊」南方へ！|5-1ボスS勝利1|要(戦艦or航戦)3/軽巡1　巡戦禁止、戦艦追加禁止
|265|海上護衛強化月間|1-5ボスA勝利10
|264|「空母機動部隊」西へ！|4-2ボスS勝利1|要(空母or軽母)2/駆逐2
|266|「水上反撃部隊」突入せよ！|2-5ボスS勝利1|要駆逐旗艦、重巡1軽巡1駆逐4
|303|「演習」で練度向上！|演習3
|304|「演習」で他提督を圧倒せよ！|演習勝利5
|302|大規模演習|演習勝利20
|402|「遠征」を3回成功させよう！|遠征成功3
|403|「遠征」を10回成功させよう！|遠征成功10
|404|大規模遠征作戦、発令！|遠征成功30
|410|南方への輸送作戦を成功させよ！|「東京急行」「東京急行(弐)」成功1
|411|南方への鼠輸送を継続実施せよ！|「東京急行」「東京急行(弐)」成功6
|503|艦隊大整備！|入渠5
|504|艦隊酒保祭り！|補給15回
|605|新装備「開発」指令|開発1
|606|新造艦「建造」指令|建造1
|607|装備「開発」集中強化！|開発3
|608|艦娘「建造」艦隊強化！|建造3
|609|軍縮条約対応！|解体2
|619|装備の改修強化|装備改修1(失敗可)
|613|資源の再利用|廃棄24回
|702|艦の「近代化改修」を実施せよ！|改修成功2
|703|「近代化改修」を進め、戦備を整えよ！|改修成功15

#### 任務の特殊仕様について

* デイリー開発・建造任務はカウンタが共用。2回目の操作(3回任務の1回目)を行った時点で50%になる
* 東京急行遠征任務も共用。7回とあるが6回でok
* 輸送船5隻の変則デイリーと輸送船3隻のデイリーはカウンタが干渉。1隻撃沈で2増える
* あ号作戦の「出撃」は文字通りであり、戦闘を行う必要はない(ex. 6-1で戦艦2を含めて即帰投しても増える)
