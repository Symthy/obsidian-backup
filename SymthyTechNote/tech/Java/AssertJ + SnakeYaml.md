

```yaml
server:
  type: default
  xxx: xxx
  applicationConnectors:
  - type: https
    xxx: xxx
	supportedCipherSuites: [aaa, bbb, ccc]
  yyy: yyy
```


```java
Map<String, Object> config = new Yaml.load(
    new ByteArrayInputStream(Files.readAllBytes(yamlPath)));

assertThat(config)
    .extracting("server")
    .asInstanceOf(InstanceOfAssertFactories.map(String.class, Object.class))
    .extracting("applicationConnectors")
    .asInstanceOf(InstanceOfAssertFactories.list(Map.class))
    .hasSize(1)
    .first()
    .asInstanceOf(InstanceOfAssertFactories.map(String.class, Object.class))
    .extracting("supportedCipherSuites")
    .asInstanceOf(InstanceOfAssertFactories.list(String.class))
    .containsExactlyInAnyOrder("aaa", "bbb", "ccc")
```


```java
import org.yaml.snakeyaml.Yaml;
// : 略

class ConfigLoader {

  public ServerConfig load(String serverConfigFilePath) throws IOException {
    Constructor constructor = new Constructor(ServerConfig.class, new LoaderOptions());
    TypeDescription typeDescription = new TypeDescription(ServerConfig.class);
    constructor.addTypeDescription(typeDescription);
    Yaml yaml = new Yaml(constructor);
    try (BufferedReader reader = Files.newBufferedReader(Paths.get(serverConfigFilePath), StandardCharsets.UTF_8)) {
      return yaml.load(reader);
    }
  }
}
```