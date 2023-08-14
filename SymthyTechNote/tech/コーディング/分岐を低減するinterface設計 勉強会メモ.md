
勉強会メモ
- 2023/3/3 エンジニア文化祭
- 分岐を低減するinterface設計と発想の転換

以下を多分に引用

ref: [Speaker Deck - 分岐を低減するinterface設計と発想の転換](https://speakerdeck.com/minodriven/interface-design-idea)

## 前提：題材とする仕様

![[interface設計_0_題材仕様.png]]

##  既存ロジックに分岐をねじ込むのはNG


顧客によって仕組みのニーズはバラバラ。
顧客ニーズの吸収するために

既存存ロジックに分岐をねじ込む ＝ NG
```
if (赤外線センサー.有効()) && 赤外線センサー.検知()) {
  if (SMS通知.有効()) {
    SMS通知.実行();
  }
  if (メッセージアプリ通知.有効()) {
    メッセージアプリ.実行()
  } else if (窓ガラス破損.有効() && 窓ガラス破損.検知()) {
    if (SMS通知.有効()) {
      SMS通知.実行();
    }
  }
  if (メッセージアプリ通知.有効()) {
    メッセージアプリ通知.実行()
  }
}
```

Interfaceを活用することで、分岐低減につながる

## Interface設計

Interface設計の要点２本柱
- 目的単位で抽象化すること
- 「作る」と「使う」を分ける

システムは目的達成の手段
- その構成要素たるクラスもまた目的達成手段

![[interface設計_1_目的達成手段.png]]

### １．目的単位で抽象化

#### 目的の具体化（ツリー構造化）

目的→課題→対策(解決策)
- 目的が決まると課題が浮き彫りになる
- 課題に対処するための解決手段が決まる
- 目的が違うと課題も解決手段も違ってくる

![[interface設計_2_構造ツリー.png]]

![[interface設計_3_構造ツリー2.png]]


#### 目的達成手段ベースで Interface設計流れ

前提：
- Interface＝目的達成手段を抽象化したもの
- Class = 具体的手段

![[interface設計_4_目的達成手段ツリー.png]]

1. 仕様を目的別に分類

![[interface設計_5_仕様の分類.png]]

2. 目的で抽象化

通知目的
![[interface設計_6_目的を抽象化.png]]

侵入検知目的
![[interface設計_7_目的を抽象化2.png]]

映像分析目的
![[interface設計_8_目的を抽象化3.png]]
![[interface設計_9_目的を抽象化4.png]]

全体
![[interface設計_10_全体構成.png]]

### ２．「作る」と「使う」を分ける

良くないロジックは、「何を使うかを判断する分岐」と「実行するロジック」が混在（あみだくじ状態）

- 「作る」側
	- どの部品（interface実装クラス）を使用するかの判断はFactoryやDIコンテナに任せる
	- 部品選択と組み立て（インスタンス生成）は「作る」側にカプセル化
- 「使う」側
	- どの部品（interface実装クラス）が使われているかは一切気にせず判断もしない
	- 渡されたインスタンスの実行だけに徹する

#### クラスとコード

通知
```java 
// 通知手段（インターフェース）
interface Alarmable {
  void alarm();
}
```

```java
// 使う側
// ファーストクラスコレクション。 destination:行き先
class AlarmDestinations {
  private final List<Alarmable> alarmDestinations;
  AlarmDestinations() {
    alarmDestinations = new ArrayList<>();
  }
  void add(Alarmable alarmDestination) {
    alarmDestinations.add(alarmDestination);
  }
  void alarm() {
    // 実態が何であるかは知らないが、ただ実行するだけ
    alarmDestinations.forEach(Alarmable::alarm);
  }
}
```

```java
// 作る側
class AlarmDestinationsFactory {
  static AlarmDestinations create() {
    AlarmDestinations alarmDestinations = new AlarmDestinations();
    // どの実装クラスを使うか判断して組み込む
    if (SmsAlarm.isEnabled()) {
      alarmDestionations.add(new SmsAlarm());
    }
    if (SmartphoneAppAlarm.isEnabled()) {
      alarmDestionations.add(new SmartphoneAppAlarm());
    }
    
    return alarmDestinations;
  }
}
```

侵入検知

```java
// 侵入検知（インターフェース）
interface TrespassDetectable {
  boolean detect();
}
```

```java
// 使う側
// 侵入検知Interfaceのファーストクラスコレクション
class TrespassDetectors {
  private final List<TrespassDetectable> detectors;
  TrespassDetectors() {
    detectors = new ArrayList<>();
  }
  void add(TrespassDetectable detector) {
    detectors.add(detector);
  }
  boolean detect() {
    // 中身は気にしない。ただ実行するだけ
    return detectors.stream().anyMatch(TrespassDetectable::detect);
  }
}
```

```java
// 作る側
class TrespassDetectorsFactory {
  static TrespassDetectors create() {
    TrespassDetectors trespassDetectors = new TrespassDetectors();

    if (InfraredSensor.isEnabled()) {  // 赤外線センサー
      trespassDetectors.add(new InfraredSensor());
    }
    if (GlassDestructionSensor.isEnabled()) {  // ガラス破損センサー
      trespassDetectors.add(new GlassDestructionSensor());
    }
    if (MonitorCamera.isEnabled()) {  // 監視カメラ
      PatternDetectors patternDetectors = PatternDetectorsFactory.create();
      trespassDetectors.add(new MonitorCamera(pattenDetectors());
    }

    return trespassDetectors;
  }
 }
```

侵入検知のうちの映像分析
```java
class MonitorCamera implements TrespassDetectable {
  private final VideoRecoder videoRecorder;  // 映像記録
  private final PatternDetectors patternDetectors;  // パターン検知

  MonitorCamera(PatternDetectors patternDetectors) {
    videoRecorder = new VideoRecorder();
    this.patternDetectors = patternDetectors;
  }
  public boolean detect() {
    // ただ検知を実行するだけ。※階層構造であろうと同様にできる
    return patternDetectors.detect();
  }
}
```

全体のトップクラス
```java
// 使う側
class HomeSecurityService {
  private final TrespassDetectors trespassDetectors;
  private final AlarmDestinations alarmDestinations;

  HomeSecurityService(
      TrespassDetectors trespassDetectors,
      AlarmDestinations alarmDestinations) {
    this.trespassDetectors = trespassDetectors;
    this.alarmDestinations = alarmDestinations;
  }

  void execute() {
    // どんな侵入検知機能や通知機能を持っているかは知らない。
    // 侵入検知して通知するだけ
    if (trespassDetectors.detect())  {
      alarmDestinations.alarm();
    }
  }
}
```

```java
// HomeSecurityService の初期化
var trespassDetectors = TrespassDetectorsFactory.create();
var alarmDestinations = AlarmDestinationsFactory.create();

HomeSecurityService homeSecurityService = new HomeSecurityServcie(
  trespassDetectors, alarmDestinations); 
```

### 目的達成手段のバリエーションと機能性

（私生活同様）状況に応じて最適な手段（interface実装クラス）を選択

- interface設計可能な箇所は、機能性向上の可能性を示唆
	- 同一目的でも、より顧客にリーチする機能（クラス）に置き換えられる可能性を秘めている

その可能性を秘めている個所、例えば
- 仕様変更で頻繁にコード変更をしている個所
	- モードやバリエーションを切り替えるための分岐が複雑化している個所

そうした箇所は、整理してinterfaceにすると、
- より高機能なものへ素早く置き換えられる
- ＝開発生産性が高まる

## まとめ

- システムは目的達成手段。その構成要素たるクラスやinterfaceも目的達成手段
	- interfaceは目的単位で抽象化
	- interface実装クラスは同一目的に対する達成手段の**バリエーション**
- interfaceを用いる場合は、「作る」側と「使う」側を分ける
	- 「作る」側は、どのinterface実装クラスを用いるかの判断を FactoryやDIコンテナとして持たせ、すべてを組み込んだ完成品を生成する
	- 「使う」側は、完成品をただ実行するだけのシンプルな構造にする。部品(interface実装クラス)が何であるかは気にしない
- interface設計した箇所はより高機能なものへ置換できる可能性を秘めている
- interfaceは銀の弾丸ではない。基本的には「手段のバリエーション」という観点での設計を推奨

目的達成手段を interface として定義。具体的手段(ロジック含む) を interface実装クラスで定義

具体的手段を条件に応じて、切り替えることで、柔軟性と高機能性を得られる？（Strategyパターン）

