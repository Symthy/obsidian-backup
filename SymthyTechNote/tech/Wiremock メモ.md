
https://wiremock.org/docs/running-standalone/

--no-request-journal：
- リクエストジャーナルは、受信したリクエストを記録して後で検証するためのものです。これにより、ヒープを使い果たすことなく、WireMockを長期間（リセットせずに）実行（およびスタブを提供）することができます。このオプションを指定すると、--record-mappingsオプションは使用できません。

```
MOCK_HTTP_PORT=44444
MOCK_HTTPS_PORT=55555
DATA_DIR=<リクエストボディとマッピングのあるフォルダのパス>
WM_LIB=～/wiremock/lib/wiremock-standalone-x.x.x.jar
WM_CLASS=com.github.tomakehurst.wiremock.standalone.WireMockServerRunner

java --classpath ${WM_LIB} ${WM_CLASS} --verbose --no-request-journal --port ${MOCK_HTTP_PORT} --https-port ${MOCK_HTTPS_PORT} --root-dir=${DATA_DIR} > /dev/null
```