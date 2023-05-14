
## フィールドのNotNull化

※openapi-generator v4.1.3

yamlから自動生成したJavaクラスのフィールドに @NotNull を付与したい場合は
yaml の required に対象のフィールドを記載する必要あり

```yaml
  properties:
    timestamp:
      #略
    id:
      #略
    data:
      #略
  required:
    - timestamp
    - id
```