
https://speakerdeck.com/tockn/prisma-ormwo2nian-yun-yong-sitepei-tutanouhauwogong-you-suru

![](images/image_20240511115448.png)

複数のSQLを生成して取得
![](images/image_20240511115752.png)

![](images/image_20240511115824.png)

![](images/image_20240511115934.png)

![](images/image_20240511115952.png)

![](images/image_20240511120012.png)

![](images/image_20240511115903.png)


![](images/image_20240511120036.png)

join 使える
![](images/image_20240511120104.png)

![](images/image_20240511120204.png)

→ 独自の Prisma Client Wrapper を用意している
tx = TransactionClient
![](images/image_20240511120225.png)

![](images/image_20240511120329.png)

![](images/image_20240511120430.png)


権限によるアクセス制御は

![](images/image_20240511120523.png)

![](images/image_20240511120605.png)![](images/image_20240511120624.png)
![](images/image_20240511120638.png)
![](images/image_20240511120652.png)

独自 Clinet に機能を入れている
![](images/image_20240511120723.png)![](images/image_20240511120738.png)
![](images/image_20240511120806.png)

列のフィルターは zod を使って絞っている

内製テストランナーをOSS化
![](images/image_20240511121102.png)

![](images/image_20240511121136.png)

オブザーバビリティ

Prismaの裏の処理：
- DBとのコネクション確立
- PrismaClientからPrismaEngine向けクエリの変換
- SQL発行
- DBからの結果をPrismaClientの結果として変換

![](images/image_20240511121352.png)

![](images/image_20240511121420.png)

![](images/image_20240511121443.png)

