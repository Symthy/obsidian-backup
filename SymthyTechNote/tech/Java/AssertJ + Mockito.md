
```java
@Test
void getConfig() {
  Config config = mock(Config.class);
  when(parser.parse(any())).thenReturn(config);

  Interable<Confing> config = factory.getConfig();

  assertThat(config).hasSize(1);
  assertThat(config).first().isSameAs(config);
  ArgumentCaptor<Properties> argCaptor = ArgumentCaptor.forClass(Properties.class);
  verify(parser, times(1)).parse(argCaptor.capture());
  assertThat(argCaptor.getValue()).containsExactlyInAnyOrderEntries(Map.of("key", "value"));
}
```