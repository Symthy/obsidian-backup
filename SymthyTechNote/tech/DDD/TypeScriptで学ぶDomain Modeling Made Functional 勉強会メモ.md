
2023/3/6

公開資料
- https://github.com/kawasima/dmmf-ts
- https://scrapbox.io/kawasima/Totality

## DMMF （型で表現）

型を使って業務を表現していくのが話のメイン

```typescript
// トランプ
type Suit = "Club" | "Diamond" | "Spade" | "Heart"
type Rank = "Two" | "Three" | "Four" | "Five" | "Six" | "Seven" | "Eight" | "Nine" | "Ten" | "Jack" | "Queen" | "King" | "Ace"

type Card = [Suit, Rank]
type Hand = Card[]  // 手札
type Deck = Card[]  // デッキ

type Player = { name: string; hand: Hand }
type Game = { deck: Deck; players: Player[] }

// 振る舞い
type Deal = (deck: Deck) => [ Deck, Card ]  // 山札からカードを配る
type PickupCard = (hand: Hand, card: Card) => Hand  // 配られたカードを手札に加える

```

関数で振る舞いを表現

![[Pasted image 20230306211425.png]]

![[Pasted image 20230306211446.png]]

Domain エキスパートとの対話

```typescript
type Deck = Card[]
type Deal = (deck: Deck) => [ Deck, Card ]
```
Domain エキスパート「これは正しくない。配るときにはシャッフルされたものでないといけない」

```typescript
type ShuffledDeck = Card[]
```
シャッフルされた山札とは？  カード集合
※Deckとは別に定義する

```typescript
type Shuffle = (deck: Deck) => ShuffledDeck
```
シャッフルされた山札を作るには？ シャッフルすればいい

型をベースに会話して型を洗練させていく＝ドメイン駆動
ここでは、
- 外部キーが何かなどの永続化に関するものは必要ないし
- オブジェクト指向設計のように、基底クラスやマネージャ、ファクトリも必要ない

ドメインのコードは、現実世界の用語をそのまんま反映させる

特に関数の定義にネームスペースが必要な言語（Java）で、ドメイン駆動のモデリングを行おうとすると
- 振る舞いを interface に含めるなど必要になり、そこ(interfaceでまとめなきゃいけない…) に引きずられる
 ![[Pasted image 20230306212718.png]]

それを取り除いて、メンタルモデルを共有するプロセスが重要 = DMMF


```typescript
type Contact = {
  firstName: string;
  middleInitial: string;
  lastName: string;
  emailAddress: string;
  isEmailVerified: boolean;
}
```
![[Pasted image 20230306212956.png]]
↓

```typescript
type String50 = string; // 50文字以内の文字列
type String1 = string;  // 1文字の文字列
type Contact = {
  Name: PersonalName:
  Email: EmailContactInfo;
}
type PersonalName = {
  firstName: String50;
  middleInitial: String1 | undefied;
  lastName: String50;
}

const createString50: CreateString50 = s => {
  if (s !== null && s.length !== 0 && s.length <= 50) {
    return s;
  } else {
    return ???;
  }
}
```

これでは制約が弱い（CreateString50を使わなければ 50文字越えの物を代入できる）

↓

Zod を使うことで Wrapper型をそこそこ安全に運用できる
```typescript
const String50 = z.string().min(1).max(50).brand("String50")
type String50 = z.infer<typeof String50>

String50.parse("")  // Parse Error
String50.parse("abc")  // String50 of abc
// String50.parse を呼ばないと、String50型が作れない
```

Zod に従って
```typescript
const String50 = z.string().min(1).max(50).brand("String50")
type String50 = z.infer<typeof String50>
const String50 = z.string().min(1).max(1).brand("String1")
type String1 = z.infer<typeof String50>
```
![[Pasted image 20230306213915.png]]

フラグをUNIONに置き換える（別の型で定義する）
![[Pasted image 20230306214051.png]]

※ついていけなかった


業務上の振る舞いの違いに着目する必要がある
→ 違うじゃんとなれば、型を分ける＝洗練する
![[Pasted image 20230306214343.png]]
※typescriptにはパターンマッチがないので、ts-match?を使ってそれっぽくしている


![[Pasted image 20230306214557.png]]
さっきのパターンマッチもいらなくなる

## Totality（全域性）

- ハンドリングできる
- 停止しない？


Type safety back and forth
割り算には全域性がない（0除算できないから）その１が使えない場合

```typescript
// 方式その２
type Divide = (a: number, b: number) => number | undefied
export const divide: Divide = (a, b) => {
  if (b !== 0) {
  }
}
// 方式その３
```

![[Pasted image 20230306215501.png]]
3点目は、型そのものが持つメリット

#DDD 
#勉強会