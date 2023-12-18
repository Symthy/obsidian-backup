
by ChartGPT

### 1. **省略されるreturn**
   - Rubyでは、メソッドの最後の式の値が自動的に返されます。これにより、`return` キーワードを省略することが一般的です。
   - 明示的に `return` を使用することも可能ですが、通常は必要ありません。

     ```ruby
     def add(a, b)
       a + b  # 明示的なreturnは不要
     end
     ```

### 2. **真偽値の扱い**
   - Rubyでは、`false` と `nil` 以外はすべて真とみなされます。
   - これは他の言語（特に `0` が偽とみなされる言語）とは異なる点です。

### 3. **ブロックとイールド**
   - メソッドにブロックを渡し、そのメソッド内で `yield` を使ってブロックを呼び出すことができます。
   - これはRubyの非常に強力な機能で、イテレータやコールバックを簡単に実装できます。

     ```ruby
     def my_method
       yield  # ブロックを呼び出す
     end

     my_method { puts "Hello from the block!" }
     ```

### 4. **メソッド名に記号を含む**
   - Rubyでは、メソッド名に `?` や `!` を含めることができます。
   - `?` で終わるメソッドは通常、真偽値を返すことを意味します（例: `empty?`）。
   - `!` で終わるメソッドは、呼び出し元のオブジェクトを変更する可能性があることを示します（例: `reverse!`）。

### 5. **シンボル**
   - シンボルは、文字列に似ていますが、メモリ効率が良く、同じシンボルはアプリケーション全体で一意です。
   - シンボルは `:` に続く名前で表されます。ハッシュのキーとしてよく使用されます。

### 6. **特異メソッド（シングルトンメソッド）**
   - Rubyでは、特定のオブジェクトに対してだけ定義されるメソッドを作成できます。
   - これらは他のどのオブジェクトにも影響を与えません。

     ```ruby
     obj = "Hello"
     def obj.shout
       self.upcase + "!"
     end

     obj.shout  # => "HELLO!"
     ```

### 7. **オープンクラス**

   - 既存のクラスに新しいメソッドを追加したり、既存のメソッドをオーバーライドしたりできます。
   - この機能は非常に強力ですが、予期しない副作用を引き起こす可能性もあるため、注意が必要です。


### 8. **条件式の後置**
   - 条件式を文の後ろに置くことができます。これによりコードが読みやすくなることがあります。
     ```ruby
     puts "Big number!" if number > 100
     ```

### 9. **範囲オブジェクトとしてのCase文**
   - `case`文は範囲オブジェクトにも対応しています。
     ```ruby
     case score
     when 90..100
       puts "Excellent"
     when 70..89
       puts "Good"
     else
       puts "Try harder"
     end
     ```

### 10. **デフォルト引数値**
   - メソッド定義で引数にデフォルト値を設定できます。
     ```ruby
     def greet(name = "World")
       puts "Hello, #{name}!"
     end
     ```

### 11. **スプラット演算子**
   - 配列を引数リストに展開したり、複数の引数を配列として受け取ったりするのに使います。
     ```ruby
     def sum(*numbers)
       numbers.reduce(:+)
     end
     ```

### 12. **ハッシュの省略記法**
   - メソッドの引数としてハッシュを渡す場合、波括弧 `{}` を省略できます。
     ```ruby
     def create_user(name:, email:)
       # ...
     end

     create_user(name: "Alice", email: "alice@example.com")
     ```

### 13. **モジュールのミックスイン**
   - モジュールをクラスにミックスインしてメソッドを追加します。
     ```ruby
     module Loggable
       def log(text)
         puts "[LOG] #{text}"
       end
     end

     class Product
       include Loggable

       def title
         log "title is called."
         "A great movie"
       end
     end
     ```

### 14. **シンボルと文字列の変換**
   - シンボルを文字列に変換するのは簡単ですが、文字列からシンボルへの変換は `to_sym` メソッドを使用します。
     ```ruby
     :my_symbol.to_s
     "my_string".to_sym
     ```

### 15. **ブロックをメソッド引数として受け取る**
   - ブロックを明示的に引数として受け取ることができます。
     ```ruby
     def my_method(&block)
       block.call
     end
     ```

### 16. **`Enumerable`モジュール**
   - `Enumerable` モジュールは、集合に対する強力な繰り返し処理を提供します。
   - `map`、`select`、`reject`、`reduce` などのメソッドが含まれます。

### 17. **動的なメソッド定義**
   - `define_method` を使用して、実行時にメソッドを定義できます。
     ```ruby
     class MyClass
       define_method :my_method do |arg|
         puts "Hello, #{arg}!"
       end
     end
     ```

### 12. **メタプログラミング**
   - Rubyはメタプログラミングが非常に強力です。