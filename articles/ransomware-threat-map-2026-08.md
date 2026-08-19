---
title: "「面の制圧」を実行しているのは誰か —— ランサムウェア勢力図で読む、いま伸びている攻撃グループの正体"
emoji: "🗺️"
type: "idea"
topics: ["security", "ransomware", "threatintelligence", "supplychain"]
published: true
---

前回の記事[「日本企業を狙うランサムウェアの本当の標的は『取引先』」](https://zenn.dev/halotaro/articles/supply-chain-ransomware-jp2026)では、攻撃者の戦略が「1社を精密に狙う」から「つながった中小企業を面で制圧する」へと転換し、日本の階層的な下請け構造がその「理想的な狩場」になっている構造を説明した。

だが、抽象的な戦略論だけでは取引先のリスク評価には使えない。「面の制圧」を実際に実行しているのは、具体的にどのグループなのか。いま勢力を伸ばしているのはどこで、逆に静かになったグループは本当に消えたのか。

この問いに答えるため、今回は筆者が個人で運用する脅威インテリジェンス基盤「RECON」の観測データを使う。Tor経由でランサムウェアリークサイトを直接収集し、公開されているランサムウェア観測プロジェクト「ransomwatch」や情報サイト「ransomware.live」のデータも取り込んだ独自データベースには、378グループ・48,327件の被害投稿が蓄積されている。2026年8月2日時点のスナップショットから、直近3か月の増減率を集計した。

ただし、この種のデータには落とし穴がある。「観測数が多い」ことと「実害が大きい」ことは同じではないし、リークサイトの被害者数はそもそも攻撃者側の自己申告に過ぎない。以下ではまず、そのデータの性質と限界を先に明らかにしたうえで、個々のグループの動きを外部の脅威インテリジェンスレポートと突き合わせて検証する。

---

## データの性質と限界を先に明らかにする

**集計方法**: RECONがその被害投稿を検知・記録した日時を基準に、直近3か月(2026年6〜8月、8月はスナップショット時点で2日分のみ)と、その前3か月(2026年3〜5月)を比較した増減率を算出している。この「検知日時」は実際の侵害発生日ではなく、あくまでリークサイトへの掲載をRECONのスクレイパーが見つけてデータベースに書き込んだタイミングである。リークサイト側が予告する掲載日(数日先の日付になっていることもある)ともズレるが、そのズレは通常数分〜数時間程度で、月次集計の粒度では実用上問題にならない。

**確認できた2つのデータ汚染**:

1. 2026年5月、外部の公開データセット(ransomwatch)から過去分をまとめて取り込んだ際、投稿日時の記録がないデータの一部が「取り込みを実行した日」(2026年5月)として扱われてしまい、その月だけ被害件数が実態の18倍以上(16,978件)に見える汚染が発生していたことを確認した。今回の集計はこの汚染分を取り除き、RECONが実際に投稿を検知した日時にもとづく値のみを使用している。
2. 2026年7月に新しい観測ソース(ransomlook)を追加し、Tor経由での直接収集の頻度も強化した。そのため直近月ほど収集網が広く、月次推移の絶対値の増加分には収集能力向上の影響が混ざっている。グループ間の相対比較(同一期間内での順位)はこの影響を受けにくいため、本稿では主に相対順位・増減率を根拠として使う。

**リークサイトの数字は「自己申告」である**: 例えば後述するCMD Organizationは自らのリークサイトで約30件の被害を主張しているが、独立系リサーチャー(WatchGuard)が確認できたのはそのうち4件程度に留まる。攻撃者は交渉を有利に進めるために被害件数を誇張するインセンティブを持つ。したがって本稿では、可能な限り複数の外部ソース(Halcyon、Check Point、Black Kite、Group-IB等)で裏付けが取れたグループを中心に論じる。

以上を踏まえたうえで、実際の勢力図を見ていく。

---

## 勢力図:直近3か月で伸びているのは誰か

直近3か月(6〜8月)の被害投稿数トップ6グループと、その前3か月との比較は次の通り。

| グループ | 直近3か月 | その前3か月 | 増減率 |
|---|---:|---:|---:|
| **The Gentlemen** | 582 | 226 | **+158%** |
| Qilin | 519 | 363 | +43% |
| Deadlock | 190 | 0 | (後述) |
| DragonForce | 160 | 179 | -11% |
| Akira | 131 | 167 | -22% |
| INC Ransom | 125 | 127 | -2% |

伸び率だけで見ると、母数の小さいグループほど数字が跳ねやすい。増減率上位を対象を絞って見ると次のようになる(直近3か月が8件以上、その前が3件以上のグループに限定)。

| グループ | 直近3か月 | その前3か月 | 増減率 |
|---|---:|---:|---:|
| Morpheus | 14 | 3 | +367% |
| CMD Organization | 77 | 19 | +305% |
| Space Bears | 44 | 13 | +238% |
| Nova | 114 | 37 | +208% |
| Securotrop | 12 | 4 | +200% |
| Aurora | 28 | 10 | +180% |
| Anubis | 52 | 20 | +160% |
| Stormous | 26 | 10 | +160% |
| The Gentlemen | 582 | 226 | +158% |
| RansomHouse | 22 | 9 | +144% |

ここで重要なのは、伸び率トップ10のうち8つが、一般的なセキュリティ担当者がまず名前を知らないであろうグループだという点だ。Morpheus、Securotrop、Space Bears、Aurora、Stormous——これらは大手ベンダーの脅威インテリジェンスフィードや取引先評価シートに載っていないことが多い。「知っている名前だけを警戒する」というアプローチが、この時点ですでに機能不全に陥っていることが分かる。

以下、外部ソースで裏付けが取れた主要グループを個別に見ていく。

---

## The Gentlemen:「記録的な速さで拡大している」という評価が、RECONの観測でも裏付けられた

RECON独自データで直近3か月の被害投稿数が最大(582件)、増減率も+158%と伸びているThe Gentlemenは、2025年半ばに出現した比較的新しいRaaS(Ransomware-as-a-Service)グループだ。

外部の脅威インテリジェンス機関の評価はRECONの観測と符合する。脅威分析企業Halcyonは同グループを「記録に残る中でほぼ最速のペースで拡大しているランサムウェア組織」と評価しており、2026年1月に48件、2月に91件とほぼ倍増するペースで攻撃を実行、2026年第2四半期には最も活発な脅威アクターの座に登りつめたと報告している。[Halcyon: Threat Assessment - The Gentlemen Ransomware Group](https://www.halcyon.ai/ransomware-research-reports/threat-assessment-the-gentlemen-ransomware-group) 2026年前半だけで328件の被害を主張し、これは同時期の全世界のランサムウェア被害申告の約10%に相当するという。[The Cyber Express: The Gentlemen Ransomware Group's Global Rise In H1 2026](https://thecyberexpress.com/the-gentlemen-ransomware-group/)

技術的には、Windows・Linux・NAS・BSD・ESXiそれぞれに対応した個別のロッカーを持ち、1回の侵入でインフラ全体を暗号化できる点が特徴だ。[The Hacker News: The Gentlemen Ransomware Claims 478 Victims, Can Spread Like a Worm](https://thehackernews.com/2026/06/the-gentlemen-ransomware-claims-478.html) 標的セクターは製造業・建設業・医療・政府機関・IT——前回記事で挙げた4セグメント（流通・SIer系、製造業部品メーカー、地方建設下請け、大手企業の地方支社）とほぼそのまま重なる。[The Insurer: The Gentlemen ransomware group emerges as most active threat actor in Q2](https://www.theinsurer.com/cyber-risk/news/the-gentlemen-ransomware-group-emerges-as-most-active-threat-actor-in-q2-2026-07-24/)

外部レポートが描くのは2026年上半期(1〜6月)までの姿だが、RECONが観測した直近3か月(6〜8月)の582件という数字は、この勢いが第3四半期に入っても衰えていないことを示している。1つの企業の一次データと、複数の独立した外部レポートが同じ方向を指しているという点で、この結論は比較的信頼できると考えている。

## Qilin:抽象的な「面の制圧」の、最も具体的な実例

RECONデータで2番目に多い直近3か月519件を記録したQilinは、2025年から続く記録的なペースの延長線上にある。Black Kiteの調査では2026年に入って以降、世界で7,551件の被害・146の活動グループを追跡しており、Qilinは前年同期比で443%の急増を見せているという。[GBHackers: 2026 Ransomware Report](https://gbhackers.com/2026-ransomware-report/) ※この443%は前年同月比の数字であり、本稿のRECONデータ(直近3か月とその前3か月を比べた値)とは比較する期間の取り方が異なるため単純比較はできないが、「Qilinの活動が拡大している」という方向性は一致する。

Qilinのビジネスモデルは典型的なRaaSで、実行役の「アフィリエイト」が身代金の80〜85%を受け取り、Qilin運営側が残りを得る。[Group-IB: Qilin Ransomware: Tactics, Attack Methods & Mitigation Strategies](https://www.group-ib.com/blog/qilin-ransomware/) そして標的セクターの23%が製造業に集中しており、卸売・物流業も頻出する——これはサプライチェーンの寸断そのものを狙う設計だ。[MoxFive: Qilin Ransomware 2026: TTPs, Victims and Defense Guide](https://www.moxfive.com/blog/qilin-ransomware-2026-ttps-victims-and-defense-guide)

そして、これは他人事ではない。2025年9月、Qilinは日本のアサヒグループホールディングスへの攻撃を実行した。ネットワーク機器を侵害してデータセンターに侵入し、複数のサーバーを暗号化。受発注・出荷・カスタマーサービスのシステムが停止し、国内30工場の大半で生産が止まった。[Citalid: Qilin & Asahi: When A "Standard" Ransomware Attack Shuts Down 30 Factories](https://citalid.com/resources/qilin-asahi-when-a-standard-ransomware-attack-shuts-down-30-factories/) アサヒ社は約50億円(3,140万ドル)の減収を見込むと発表し、システムの完全復旧には2か月を要した。[Industrial Cyber: Qilin hackers claim responsibility for Asahi cyberattack](https://industrialcyber.co/ransomware/qilin-hackers-claim-responsibility-for-asahi-cyberattack-allege-theft-of-27-gb-of-data-amid-ongoing-investigation/) 従業員・退職者・取引先など約190万件の個人情報流出も確認されている。[Rescana: Asahi Group Holdings Ransomware Attack](https://www.rescana.com/post/asahi-group-holdings-ransomware-attack-qilin-breach-disrupts-japanese-operations-and-exposes-1-5-mi)

前回記事で「製造業の部品メーカーは生産システムそのものが停止して親企業の稼働率が下がる」というシナリオを抽象的に説明したが、アサヒの事例はまさにその実例だ。しかもアサヒは中小の下請けではなく大企業本体である——「面の制圧」戦略の対象は中小企業に限らず、サプライチェーンのハブとなる大企業本体にも及ぶことを、この事例は示している。

## Deadlock:「消えた」グループが最新の技術で戻ってくる

RECONデータでDeadlockは前3か月ゼロから直近3か月で190件と、最も目立つ「新規参入」に見える。だが外部レポートを確認すると、これは正確な表現ではない。Deadlockは2025年7月から活動していたが、その後11か月間リークサイト上で活動が確認できない「休眠」状態にあり、2026年6月に単月75件という規模で再浮上、その月のランサムウェアリーダーボードで2位に入った。[ReliaQuest: Ransomware and Cyber Extortion in Q2 2026](https://reliaquest.com/blog/threat-spotlight-ransomware-and-cyber-extortion-in-q2-2026)

技術的には、ブロックチェーン上でC2(指令サーバー)通信をホストする方式と、カーネルレベルでEDR(端末の異常な挙動を監視・遮断するセキュリティ製品)を無効化する脆弱なドライバの悪用を組み合わせており、防御側がブロックできるドメインやIPアドレスが存在しない。暗号化の前にそのEDR自体を止めてしまうため、検知に必要なログや通信記録そのものが残らなくなる。[ReliaQuest: 同上] 実際に2026年7月には、スペインの製造業Grupo Relesaが被害に遭ったことが確認されている。[DeXpose: Deadlock Ransomware Strikes Spanish Manufacturer Grupo Relesa](https://www.dexpose.io/deadlock-ransomware-strikes-spanish-manufacturer-grupo-relesa/)

Deadlockの11か月の「沈黙」は、休止=消滅ではないことを示す典型例だ。この点は、後段の「活動が止まったグループ」の解釈にも直結する。

## CMD Organization:急成長するグループほど、数字を割り引いて読む必要がある

RECONデータで増減率2位(+305%、19件→77件)のCMD Organizationは、2026年3月末に活動を開始したばかりの新しいグループだ。クリアネット上のリークサイト(`cmdofficial.com`)は3月29日にNamecheapで登録されており、独自の特徴として、盗んだデータを暗号資産で入札にかけられる「オークション機能」をリークサイトに実装している。[Beazley Security Labs: CMD Organization – New Ransomware Operator Moves to Place Public Bidding Wars on Ransomed Data](https://labs.beazley.security/articles/cmd-organization-new-ransomware-operator-moves-to-place-public-bidding-wars-on-ransomed-data)

ただし、ここで冒頭の注意点が効いてくる。活動開始から4か月でリークサイト上は約30件の被害を主張しているが、WatchGuardなど独立系リサーチャーが実際に確認できたのはそのうち4件程度に留まる。[WatchGuard: CMD Organization Ransomware Tracker](https://www.watchguard.com/wgrd-security-hub/ransomware-tracker/cmd-organization) つまりRECONが観測した「77件」という数字も、その相当割合が未検証の自己申告である可能性を織り込んで読む必要がある。急成長しているように見えるグループほど、実態以上に誇張されたブランディング戦略が働いている可能性を疑うべきだ。

---

## 新興グループの参入速度:エコシステムは止まらない

RECONの観測では、前3か月にゼロ件だったグループが直近3か月で新たに5件以上を記録したケースが8グループあった。

| グループ | 直近3か月の投稿数 | 初出現(RECON観測) |
|---|---:|---|
| Deadlock ※上述の通り実際は再浮上 | 190 | 2026-05-22 |
| Global Secret Group | 56 | 2026-07-26 |
| CRPXO | 53 | 2026-07-09 |
| Settra | 40 | 2026-06-02 |
| ExfilSquad | 30 | 2026-07-26 |
| Section 9 | 22 | 2026-07-26 |
| Black X | 19 | (不明) |
| Doommageddon | 19 | 2026-03-08 |

このうち3グループ(Global Secret Group、ExfilSquad、Section 9)は2026年7月26日という同じ日に初めてRECONの観測網に現れている——単一グループの偶然というより、RaaSのアフィリエイト市場が新しい「ブランド」を生み出すペースそのものを示している可能性が高い。

これは業界全体のトレンドとも一致する。Recorded Futureは2026年について、ロシア圏外で活動する新規ランサムウェアアクターの数が、ロシア圏内のそれを初めて上回る年になると予測している。[Recorded Future: New ransomware tactics to watch out for in 2026](https://www.recordedfuture.com/blog/ransomware-tactics-2026) 参入障壁も下がり続けており、2025年5月に流出したLockBitのアフィリエイトパネルのリークからは、下位ランクの「Lite」版パネルへのアクセスがわずか777ドル程度で取得できていたことが判明している。[TRM Labs: New Disruption Opportunities in the Evolving Ransomware Ecosystem](https://www.trmlabs.com/resources/blog/new-disruption-opportunities-in-the-evolving-ransomware-ecosystem) 一つのグループを摘発しても、アフィリエイトは数日で別のブランドに移動するだけだとTRM Labsは指摘している——2025年前半のRansomHub崩壊後に実際に起きたのがこのパターンだ。

つまり「新興グループが増えている」のは異常事態ではなく、RaaSモデルが持つ構造的な性質そのものだ。取引先のセキュリティ評価を「既知の脅威グループのリストに載っているか」だけで行う手法は、この参入速度に追いつけない。

---

## 「活動が止まった」は「終わった」ではない

RECONの観測では、かつて一定数の投稿があったが直近3か月は投稿ゼロになったグループも複数ある。

| グループ | その前3か月の投稿数 | 最終観測日 |
|---|---:|---|
| Handala | 47 | 2026-04-07 |
| Sinobi | 13 | 2026-05-05 |
| ALP-001 | 17 | 2026-04-08 |
| Beast | 8 | 2026-04-24 |
| Crypto24 | 8 | 2026-04-17 |
| LeakBazaar | 9 | 2026-05-09 |

これを見て「摘発された」「壊滅した」と即断するのは早計だ。ランサムウェアグループが沈黙する理由は、法執行機関の摘発だけではない。むしろ最も一般的なパターンは、注目を集めすぎたグループが意図的に活動を止め、名前を変えて再出発することだ。[Cybersecurity Dive: Identity crisis: Why ransomware groups rebrand — and disappear](https://www.cybersecuritydive.com/news/ransomware-ryuk-conti-revil-2021/608845/)

象徴的な事例がBlackCat(ALPHV)だ。2024年3月、Change Healthcareから受け取ったとされる2,200万ドルの身代金を独占し、アフィリエイトへの分配を行わずインフラを閉鎖する「exit scam」を実行して姿を消した。[The Hacker News: Exit Scam: BlackCat Ransomware Group Vanishes After $22 Million Payout](https://thehackernews.com/2024/03/exit-scam-blackcat-ransomware-group.html) BlackCatはそれ以前もDarkSide→BlackMatter→BlackCatと名前を変えてきた実績があり、GandCrabからREvilへの転生も同様のパターンとして知られている。[CPO Magazine: Under Increasing Federal Scrutiny, BlackCat Ransomware Gang Pulls Exit Scam](https://www.cpomagazine.com/cyber-security/under-increasing-federal-scrutiny-blackcat-ransomware-gang-pulls-exit-scam-on-its-way-out/)

前段のDeadlockが11か月の沈黙を経て、より高度な技術(ブロックチェーンC2、カーネルレベルEDR回避)を携えて再浮上した事例は、この「沈黙=強化期間」というパターンをそのまま体現している。取引先の評価において、「あのグループはもう活動していないから安全」という判断は、この業界の力学を無視した楽観にすぎない。

---

## 標的セクター:前回記事の指摘と符合する

RECON全期間・全グループ合算での被害セクター分布は次の通り(上位4セクター)。

| セクター | 被害件数 |
|---|---:|
| ビジネスサービス | 7,755 |
| 製造業 | 6,519 |
| テクノロジー | 4,418 |
| 医療 | 3,585 |

これは世界全体の集計であり、日本国内に限定したものではない。それでも、上位2セクター(ビジネスサービス・製造業)は、前回記事で挙げた「流通・SIer系中堅企業」「製造業の部品メーカー」というセグメントとほぼそのまま重なる。JIPDECの調査では日本国内の製造業のランサムウェア感染率が業種別で最も高い57.1%に達すると報告されており、[JIPDEC: 企業IT利活用動向調査2026](https://www.jipdec.or.jp/library/it-resarch/it-resarch2026-03.html) RECONが観測するグローバルな標的傾向とも整合する。攻撃者にとって「つながりが多く、供給網の寸断効果が大きい」セクターという条件は、国境を越えて共通しているようだ。

なお、国別の内訳ではアメリカが12,893件で突出し、日本(JP)はリークサイト上で国が特定できた投稿全体(約3万件)のうち424件、比率にして1.4%程度にとどまる。これは日本企業が狙われていないことを意味しない——前回記事で指摘した「報告・公表を遅延させる傾向」、つまり水面下で交渉を終わらせリークサイト掲載に至らないケースが多い可能性と整合的な数字であり、むしろ観測データに実態が表れにくい構造そのものを裏付けているとも読める。ただしこれはあくまで一つの解釈であり、断定はできない点は付言しておく。

---

## 前回記事のセグメント表を、今回のデータで更新する

前回記事で示した4セグメントに、今回裏付けが取れたグループを当てはめると次のようになる。

| セグメント | 前回記事の主な侵入経路 | 今回確認された関連グループ・事例 |
|---|---|---|
| 流通・SIer系中堅企業 | フィッシング・露出した管理画面 | The Gentlemen(IT・製造業を標的)、CMD Organization(教育機関を含む) |
| 製造業の部品メーカー | 生産システムとITの統合部分 | Qilin(アサヒHD実例、製造業23%集中)、Deadlock(スペイン製造業実例) |
| 地方の下請け建設・エンジニアリング | 老朽化したVPNゲートウェイ | The Gentlemen(建設業を標的セクターに明記) |
| 大手企業の地方支社・子会社 | 支社ローカルシステム | RaaSの低い参入障壁全般(アフィリエイトパネル777ドル、地理的拡散) |

抽象的だった前回のセグメント論は、今回のデータで「誰が」「どのように」実行しているかまで具体化できた。

---

## 結論:個別グループを追うのではなく、構造で守る

ここまでの分析から言えることは3つある。

第一に、いま最も活動量が大きいグループ(The GentlemenやQilin)は、複数の独立した外部レポートとRECONの一次データの両方で裏付けが取れる、実在する急拡大トレンドだ。特にThe Gentlemenは、外部レポートが対象とする2026年上半期の勢いが、RECONが観測した第3四半期に入っても衰えていないことを示している。

第二に、伸び率で見ると上位の大半は無名の小規模グループであり、これは異常ではなくRaaSエコシステムの通常運転だ。取引先評価を「知っている脅威グループ名のリスト」に依存させている限り、この参入速度には構造的に追いつけない。

第三に、リークサイトへの投稿がゼロになったグループについて「脅威が去った」と判断するのは危険だ。摘発よりも「沈黙してブランドを変える」方がはるかに一般的なパターンであり、Deadlockのように沈黙後により高度な技術で再浮上する例すらある。

これらはすべて、前回記事の結論——**個別の攻撃者ではなく「サプライチェーン全体の可視化と最小限のセキュリティ要件の徹底」に投資すべきだ**——を裏付ける形で着地する。MFA、ネットワークセグメンテーション、オフラインバックアップといった構造的な対策が有効なのは、それが特定のグループの手口ではなく、RaaSモデルそのものが持つ「侵入から展開までの共通経路」を塞ぐからだ。攻撃者の名前は入れ替わり続けるが、その経路はそう簡単には変わらない。

RECONによる観測は今後も継続する予定であり、この勢力図がどう変化するかは追って報告したい。

---

## 参考資料

- [Halcyon: Threat Assessment - The Gentlemen Ransomware Group](https://www.halcyon.ai/ransomware-research-reports/threat-assessment-the-gentlemen-ransomware-group)
- [The Cyber Express: The Gentlemen Ransomware Group's Global Rise In H1 2026](https://thecyberexpress.com/the-gentlemen-ransomware-group/)
- [The Hacker News: The Gentlemen Ransomware Claims 478 Victims, Can Spread Like a Worm](https://thehackernews.com/2026/06/the-gentlemen-ransomware-claims-478.html)
- [The Insurer: The Gentlemen ransomware group emerges as most active threat actor in Q2](https://www.theinsurer.com/cyber-risk/news/the-gentlemen-ransomware-group-emerges-as-most-active-threat-actor-in-q2-2026-07-24/)
- [GBHackers: 2026 Ransomware Report Reveals 7,551 Victims, 146 Active Groups, and Qilin's 443% Surge](https://gbhackers.com/2026-ransomware-report/)
- [Group-IB: Qilin Ransomware: Tactics, Attack Methods & Mitigation Strategies](https://www.group-ib.com/blog/qilin-ransomware/)
- [MoxFive: Qilin Ransomware 2026: TTPs, Victims and Defense Guide](https://www.moxfive.com/blog/qilin-ransomware-2026-ttps-victims-and-defense-guide)
- [Citalid: Qilin & Asahi: When A "Standard" Ransomware Attack Shuts Down 30 Factories](https://citalid.com/resources/qilin-asahi-when-a-standard-ransomware-attack-shuts-down-30-factories/)
- [Industrial Cyber: Qilin hackers claim responsibility for Asahi cyberattack](https://industrialcyber.co/ransomware/qilin-hackers-claim-responsibility-for-asahi-cyberattack-allege-theft-of-27-gb-of-data-amid-ongoing-investigation/)
- [Rescana: Asahi Group Holdings Ransomware Attack](https://www.rescana.com/post/asahi-group-holdings-ransomware-attack-qilin-breach-disrupts-japanese-operations-and-exposes-1-5-mi)
- [ReliaQuest: Ransomware and Cyber Extortion in Q2 2026](https://reliaquest.com/blog/threat-spotlight-ransomware-and-cyber-extortion-in-q2-2026)
- [DeXpose: Deadlock Ransomware Strikes Spanish Manufacturer Grupo Relesa](https://www.dexpose.io/deadlock-ransomware-strikes-spanish-manufacturer-grupo-relesa/)
- [Beazley Security Labs: CMD Organization – New Ransomware Operator Moves to Place Public Bidding Wars on Ransomed Data](https://labs.beazley.security/articles/cmd-organization-new-ransomware-operator-moves-to-place-public-bidding-wars-on-ransomed-data)
- [WatchGuard: CMD Organization Ransomware Tracker](https://www.watchguard.com/wgrd-security-hub/ransomware-tracker/cmd-organization)
- [Recorded Future: New ransomware tactics to watch out for in 2026](https://www.recordedfuture.com/blog/ransomware-tactics-2026)
- [TRM Labs: New Disruption Opportunities in the Evolving Ransomware Ecosystem](https://www.trmlabs.com/resources/blog/new-disruption-opportunities-in-the-evolving-ransomware-ecosystem)
- [Cybersecurity Dive: Identity crisis: Why ransomware groups rebrand — and disappear](https://www.cybersecuritydive.com/news/ransomware-ryuk-conti-revil-2021/608845/)
- [The Hacker News: Exit Scam: BlackCat Ransomware Group Vanishes After $22 Million Payout](https://thehackernews.com/2024/03/exit-scam-blackcat-ransomware-group.html)
- [CPO Magazine: Under Increasing Federal Scrutiny, BlackCat Ransomware Gang Pulls Exit Scam](https://www.cpomagazine.com/cyber-security/under-increasing-federal-scrutiny-blackcat-ransomware-gang-pulls-exit-scam-on-its-way-out/)
- [JIPDEC: 企業IT利活用動向調査2026](https://www.jipdec.or.jp/library/it-resarch/it-resarch2026-03.html)
- 出典: RECON Threat Intelligence Dashboard（自己運用基盤）
