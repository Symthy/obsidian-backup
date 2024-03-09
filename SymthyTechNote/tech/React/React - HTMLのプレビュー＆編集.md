  
外部から渡されるHTMLの中から文面の部分を特定して編集するには、HTMLの構造をあらかじめ決めておく必要があります。たとえば、文面の部分を特定のクラス名やIDを持つ要素に入れるなどの方法が考えられます。

```tsx
import React, { useState } from 'react';

function EmailEditor() {
  const [emailHtml, setEmailHtml] = useState(`
    <!DOCTYPE html>
    <html lang="ja">
    <head>
      <meta charset="UTF-8">
      <title>挨拶メール</title>
      <style>/* スタイルの定義 */</style>
    </head>
    <body>
      <div class="container">
        <h1 class="email-greeting">こんにちは、友人へ</h1>
        <p class="email-body">いつもお世話になっております。元気にしていますか？</p>
        <p class="email-closing">敬具<br>あなたの名前</p>
      </div>
    </body>
    </html>
  `);

  const updateEmailContent = (className, newContent) => {
    const parser = new DOMParser();
    const doc = parser.parseFromString(emailHtml, 'text/html');
    const element = doc.querySelector(`.${className}`);
    if (element) {
      element.innerHTML = newContent.replace(/\n/g, '<br>');
      setEmailHtml(doc.documentElement.outerHTML);
    }
  };

  return (
    <div>
      <div>
        <label>
          挨拶:
          <textarea
            value={emailHtml.match(/class="email-greeting">([^<]*)</)?.[1] || ''}
            onChange={(e) => updateEmailContent('email-greeting', e.target.value)}
          />
        </label>
        <label>
          本文:
          <textarea
            value={emailHtml.match(/class="email-body">([^<]*)</)?.[1] || ''}
            onChange={(e) => updateEmailContent('email-body', e.target.value)}
          />
        </label>
        <label>
          署名:
          <textarea
            value={emailHtml.match(/class="email-closing">([^<]*)</)?.[1] || ''}
            onChange={(e) => updateEmailContent('email-closing', e.target.value)}
          />
        </label>
      </div>
      <div dangerouslySetInnerHTML={{ __html: emailHtml }} />
    </div>
  );
}

export default EmailEditor;

```


`/class="email-closing">([^<]*)</)?.[1] || ''` の部分

正規表現を使用してHTML文字列から特定のクラス名を持つ要素の内容を抽出するためにあります。

- `class="email-closing">` は、クラス名が `email-closing` である要素を見つけるためのパターンです。
- `([^<]*)` は、`<` が出現するまでの任意の文字列をキャプチャするためのパターンです。これにより、開始タグと終了タグの間にある要素の内容を取得します。
- `?</` は、非貪欲マッチングを行うための記述で、できるだけ短い文字列にマッチします。
- `?.[1] || ''` は、マッチング結果が存在する場合はその1番目のキャプチャグループ（`([^<]*)` の部分）の値を取得し、存在しない場合は空文字列を返すための記述です。