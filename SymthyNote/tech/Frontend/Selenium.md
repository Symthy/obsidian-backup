
[Seleniumで画面キャプチャ・スクショを撮る（全画面対応）](http://holiday-programmer.net/selenium_screen_shot/)

```python
import os
import datetime
from selenium import webdriver

#ヘッドレス実行　※全画面用
options = webdriver.ChromeOptions()
options.add_argument('--headless')

#ブラウザ起動
driver = webdriver.Chrome(executable_path=os.getcwd() + "\\webdriver\\chromedriver.exe",options=options)

#保存する画像ファイル名
fname = datetime.datetime.now().strftime('%Y-%m-%d-%H-%M-%S')

#スクショ保存ディレクトリが存在しなければ生成
if os.path.isdir(os.getcwd()+"\\ss") == False:
    os.mkdir(os.getcwd()+"\\ss")

#テスト用URLに接続（郵便局、深い意味はないです。。。）
driver.get("https://www.post.japanpost.jp/zipcode/dl/oogaki-zip.html")

#ウインドウサイズをWebサイトに合わせて変更　※全画面用
width = driver.execute_script("return document.body.scrollWidth;")
height = driver.execute_script("return document.body.scrollHeight;")
driver.set_window_size(width,height)

#スクショをPNG形式で保存
driver.get_screenshot_as_file(os.getcwd() + "\\ss\\" + fname + ".png")
```