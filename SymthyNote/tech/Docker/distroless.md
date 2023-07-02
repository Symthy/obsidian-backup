
Googleが提供している必要最小限の依存のみが含まれるDebian10(buster)を基に作成されたコンテナイメージ
- アプリケーションとそのランタイム依存関係のみが含まれる。
	- 標準の Linux ディストリビューションに含まれるパッケージ マネージャー、シェル、その他のプログラムは含まれていない
- ランタイムコンテナーの内容をアプリに必要なものだけに制限することは、ベストプラクティス
	- スキャナー (CVE など) の S/N 比が向上し、必要なものの出所を特定する負担を軽減できる
ref:  https://github.com/GoogleContainerTools/distroless/blob/main/base/README.md

メリット：
- セキュア
	- 本当に実行に必要なのファイルのみを含んだimageとなっているので、不要なバグや脆弱性を埋め込みにくいという点が、セキュア
- 軽量
	- 含まれるファイルを最小限にしているので、imageのサイズを小さくできる
ref: [コンテナイメージ使うならdistrolessもいいよねという話](https://qiita.com/yoshii0110/items/b6fb50505767ba2c33ad)


golang × distroless は相性がいい
- golang のようにバイナリを１つor複数にまとめる物と相性がいい（環境にそれだけ置けば動く、環境設定等が不要だから？）
- 「dostroless imageを使用するとき、例えばGoでバイナリを配置するだけの場合や、Pythonで共有ライブラリを静的にリンクした成果物をコンテナ内に配置できる場合は工夫しなくても build stage と copy stage を組み合せたmulti stage buildで済む」
pythonやRubyとは相性がよくないかもれしれない
- 「PythonやRubyのようなscript言語は、実行時に必要となるライブラリ群をまとめて1つないし複数個のバイナリとして固めることができません。がんばれば実行時に必要なファイルを列挙することもできるでしょうが、その作業と実際の配置を行うのは困難です。」
ref: [distroless imageを実用する](https://blog.unasuke.com/2021/practical-distroless/)


#Docker/distroless

