
```java
class Test {
  private static final BASE_DIR_PATH = "src/test/resources/mock/response/"

  private static WireMockServer apiMock = new WireMockServer(
    wireMockConfig().port(40000).usingFilesUnderDirectory(withPath()));

  private static withPath(String path) {
    return BASE_DIR_PATH + path;
  }

  @BeforeAll
  static void beforeAll() {
    RestAssuredConfigurator.initRestAssured(ApiConfig.newInstance, RedirectMode.NOT_FOLLOW);
  }

  @AfterAll
  static void afterAll() {
    mock.stop();
  }

  @BeforeEach
  void beforeEach() {
    mock.start();
  }

  @AfterEach
  void afterEach() {
    mock.resetAll();
  }

  @Test
  void test_success() {
    RestAssured
      .given()
      .header("X-Access-Token", "dummy_token")
      .when()
      .get("/v1/objects/xxx")
      .then()
      .statusCode(307)
      .header("location", is("path"))
  }
}
```