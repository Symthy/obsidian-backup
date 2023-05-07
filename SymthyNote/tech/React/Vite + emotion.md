
```sh
npm i @emotion/react
# npm i -D @emotion/babel-plugin # 不要？
```

- vite.config.ts
```diff
export default defineConfig({
  plugins: [
-    react()
+    react({
+      jsxImportSource: '@emotion/react',
+    }),
    tsconfigPaths()
  ],
  // :
});
```

- tsconfig.json
```diff
{
  // :
+    "jsxImportSource": "@emotion/react",
  // :
}
```

 viteはコンパイル時に指定したものを無視する ref: https://zenn.dev/riemonyamada/articles/e1430eace41f79

[Vite + React.jsでEmotionを使いたい](https://koredana.info/blog/i-wanna-use-emotionjs-with-vite-reactjs/)

