
by ChatGPT

Rubyで外部システムのREST APIを実行するためには、`Net::HTTP` ライブラリや外部ライブラリ（例えば `HTTParty` や `RestClient`）を使用することが一般的です。ここでは、標準ライブラリである `Net::HTTP` を使用した例と、`HTTParty` gemを使用した例を示します。

### Net::HTTP を使用した例

`Net::HTTP` はRubyの標準ライブラリであり、追加のgemをインストールする必要はありません。

```ruby
require 'net/http'
require 'uri'
require 'json'

uri = URI.parse("https://example.com/api/resource")
request = Net::HTTP::Get.new(uri)
request["Content-Type"] = "application/json"

response = Net::HTTP.start(uri.hostname, uri.port, use_ssl: uri.scheme == "https") do |http|
  http.request(request)
end

if response.is_a?(Net::HTTPSuccess)
  result = JSON.parse(response.body)
  puts result
else
  puts "Error: #{response.code}"
end
```

このコードは、指定されたURLのREST APIにGETリクエストを送信し、応答を処理します。

### HTTParty gem を使用した例

`HTTParty` はREST APIの操作を簡素化するためのRuby gemです。まず、Gemfileに `httparty` を追加します。

```ruby
# Gemfile
gem 'httparty'
```

次に、`bundle install` を実行してgemをインストールします。

以下は `HTTParty` を使用したGETリクエストの例です。

```ruby
require 'httparty'

response = HTTParty.get("https://example.com/api/resource", headers: { "Content-Type" => "application/json" })

if response.success?
  result = response.parsed_response
  puts result
else
  puts "Error: #{response.code}"
end
```

`HTTParty` は、リクエストとレスポンスを扱う際のコードをよりシンプルにします。`Net::HTTP` と比較して、より少ないコードで同様の操作が可能です。

### 注意点
- エラーハンドリング: レスポンスのステータスコードに応じて適切なエラーハンドリングを行います。
- タイムアウト: ネットワークの遅延や障害を考慮して、タイムアウトの設定をすることを検討してください。
- セキュリティ: HTTPSを使用し、セキュアな通信を確保してください。

これらの方法を用いることで、Rubyから外部システムのREST APIを効率的に利用できます。