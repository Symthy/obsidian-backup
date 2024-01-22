
Ruby on Rails のビューに JavaScript をインポートする方法は、Rails のバージョンによって異なります。Rails 6 以降では、Webpacker が標準で使用されており、Rails 5 以前では、アセットパイプライン (Sprockets) が主に使用されています。以下に、両方の方法を説明します。

### Rails 6 以降 (Webpacker を使用)

Rails 6 以降では、JavaScript ファイルは `app/javascript` ディレクトリ内に配置され、Webpacker によって管理されます。

1. **JavaScript ファイルの作成:** まず、`app/javascript/packs` ディレクトリに JavaScript ファイルを作成します。例えば、`app/javascript/packs/custom.js` というファイルを作成できます。

2. **JavaScript ファイルのインポート:** 次に、ビューでこの JavaScript ファイルを使用するには、対応するビューファイル（例えば `app/views/layouts/application.html.erb`）に下記のタグを追加します。

   ```erb
   <%= javascript_pack_tag 'custom', 'data-turbolinks-track': 'reload' %>
   ```

   ここで `'custom'` は、`app/javascript/packs` にあるファイル名（拡張子を除く）に対応します。

