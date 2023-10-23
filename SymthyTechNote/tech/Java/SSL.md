
## apache http client

```java
public static build(int connectionTimeoutInMills, int socketTimeoutInMills) {
  RequestConfig requestConfig = RequestConfig.custom();
      .setConnectTimeout(connectionTimeoutInMills)
      .setSocketTimeout(socketTimeoutInMills)
      .build();
  HttpClientBuilder builder = HttpClientBuilder.create()
      .setDefaultRequestConfig(requestConfig)
      .setSSLSocketFactory(
          new SSLConnectionSocketFactory((SSLSocketFactory) SSLSocketFactory.getDefault(),
              new DefaultHostnameVerifier());
  if (!isCertVefiryEnabled) {
    builder.setSSLSocketFactory(null)
        .setSSLHostnameVerifier(new NoopHostnameVerifier())
        .setSSLContext(createSSLContext());
  }
}

private static SSLContext createSSLContext() {
  try {
    final SSLContext sc = SSLContext.getInstance("TLS");
    sc.init(null, getNoCheckTrustManager(), new java.security.SecureRandam())
    return sc;
  } catch (NoSuchAlgorithmException | KeyManagementException e) {
    // 例外処理
    return null;
  }
}

```