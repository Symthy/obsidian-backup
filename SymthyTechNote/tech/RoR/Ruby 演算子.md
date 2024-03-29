
by ChartGPT

### 1. **範囲演算子**
   - `..` と `...` は範囲を作成するために使われます。
   - `..` は両端を含む範囲を作り、`...` は終端を除く範囲を作ります。
     ```ruby
     (1..5)   # 1から5までの範囲（5を含む）
     (1...5)  # 1から5までの範囲（5を含まない）
     ```

### 2. **条件演算子**
   - `?:` は三項演算子で、簡単な条件式を書くのに使われます。
     ```ruby
     condition ? if_true : if_false
     ```

### 3. **安全ナビゲーション演算子（ぼっち演算子）**
   - `&.` は、メソッド呼び出し先のオブジェクトが `nil` の場合に `nil` を返します。これにより `nil` チェックを簡潔に行えます。
     ```ruby
     object&.method
     ```

### 4. **スプラット演算子**
   - `*` は配列を展開するために使われます。
   - メソッド引数で使うと、任意の数の引数を受け取ることができます。
     ```ruby
     def my_method(*args)
       args.each { |arg| puts arg }
     end
     ```

### 5. **パーセント記法**
   - `%` は特定のリテラルをより簡潔に書くために使われます。
   - 例えば、`%w` は文字列の配列を作成し、`%q` はシングルクォート文字列を作成します。
     ```ruby
     %w[one two three]  # ["one", "two", "three"]
     %q{This is 'a' string}  # "This is 'a' string"
     ```

### 6. **論理演算子の高度な使用**
   - `&&`、`||` は論理演算子ですが、Rubyでは最後に評価された式の値を返します。
     ```ruby
     a = nil
     b = "Hello"
     a && b  # nil
     a || b  # "Hello"
     ```

### 7. **等価演算子**

- `eql?`、`equal?` などでもオブジェクトの等価性をチェックできる

### 8. **ビット演算子**
   - `&`、`|`、`^`、`~`、`<<`、`>>` などのビット演算子も利用できます。

これらの演算子はRuby特有のものや他の言語にも存在するがRuby特有の使われ方をするものが含まれます。Rubyのコードを書く際にこれらの演算子の存在を知っておくと、よりRubyらしい、効率的なコードを書くことができます。

### 9.  **`||=` 演算子**

変数が未定義または `nil` の場合に値を設定します。

```ruby
a ||= 10
```