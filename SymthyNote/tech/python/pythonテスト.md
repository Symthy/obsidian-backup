before/after/モック

# 事前処理、事後処理

-   yieldの前が事前処理、後が事後処理。 yield(xxx) で渡したものはテストメソッドの引数で受け取れる
-   yield の前で例が起きると yield の後の処理は実行されない

```python
@pytest.fixture(scope='session', autouse=True)
def scope_session():
    print("setup before session")
    yield
    print("teardown after session")

@pytest.fixture(scope='module', autouse=True)
def scope_module():
    print("    setup before module")
    yield
    print("    teardown after module")

@pytest.fixture(scope='class', autouse=True)
def scope_class():
    print("        setup before class")
    yield
    print("        teardown after class")

@pytest.fixture(scope='function', autouse=True)
def scope_function():
    print("            setup before function")
    yield
    print("            teardown after function")
```

[https://qiita.com/mwakizaka/items/e51c604155633ccd33dd](https://qiita.com/mwakizaka/items/e51c604155633ccd33dd)

# Mock (pytest-mock)

関数のモックは、テストするモジュールにimportされているものを指定する必要がある

[https://webbibouroku.com/Blog/Article/pytest-mock#outline__3_6_2](https://webbibouroku.com/Blog/Article/pytest-mock#outline__3_6_2)

```python
# src/my_code
from os import remove
from os.path import isfile

# テスト対象のメソッド
def delete_file(file_path):
    try:
        if not isfile(file_path):
            return 'ファイルが存在しません。'
        
        remove(file_path)

        return 'ファイルを削除しました。'
    except:
        return 'エラーが発生しました。'
```

```python
import src.my_code as my_code

# テスト
def test_remove(mocker):
    # モック化
    isfile_mock = mocker.patch('src.my_code.isfile', return_value=True)
    remove_mock = mocker.patch('src.my_code.remove')
```

pytest-mock は unittest.mock をラップしたもの

- [【pytest】モックの使い方まとめ](https://zenn.dev/re24_1986/articles/0a7895b1429bfa)
- [[Python] pytest-mock使い方メモ](https://qiita.com/sengoku/items/9410d30a9fcb1af89832)
- [[Python] pytest でモックを使う方法(pytest-mock)](https://webbibouroku.com/Blog/Article/pytest-mock)



関数は mocker.patch

クラスは mocker.Mock(spec=xxxClass) と mocker.patch

クラスのメソッドは mocker.patch.object(xxxClass, 'methodName')


#python/test
