
```java
@Mapper
public interface EventMapper {
  @SelectProvider(EventMapperSQLProvider.class)
  List<EventRdbDto> find(@Nullable List<String> type, 
      @Nullable OffsetDateTime fromTime, @Nullable OffsetDateTime toTime);
  
  @SelectProvider(EventMapperSQLProvider.class)
  Long countAll();

  @InsertProvider(EventMapperSQLProvider.class)
  void insert(EventRdbDao event);

  @DeleteProvider(EventMapperSQLProvider.class)
  void deleteOld(int maxCount);

  @SuppressWarnings({"method.invocation", "unused"})
  class EventMapperSqlProvider implements ProviderMethodResolver {

    static final String TABLE_NAME = "events";
    static final List<String> ALL_COLUMNS = List.of(
        "time", "type", "value", "message");

    public static String find (List<String> type, 
        OfssetDateTime fromTime, OffsetDateTime toTime) {
      return new SQL() {
        SELECT(""
          + "type AS \"type\",\n"
          + "time AS \"time\",\n"
          + "value AS \"value\",\n"
          + "message AS \"message\"\n"
        );
        FROM(TABLE);
        if (Objects.nonNull(type)) {
          WHERE(SqlUtils.createInClause("type", type.size()));
        }
        if (Objects.nonNull(fromTime) && Objects.nonNull(toTime)) {
          WHERER("time >= #{fromTime} AND time <= #{toTime}");
        }
        if (Objects.nonNull(eventSort)) {
          ORDER_BY(SqlUtils.createOrderByClause(eventSort.getColumn(), eventSort.isAscending()));
        }
      }.toString();
    }

    public static String countAll() {
      return new SQL() {
        {
          SELECT("COUNT(*)");
          FROM(TABLE_NAME);
        }
      }.toString();
    }

    public static String insert(EventRdbDao event) {
      return new SQL() {
        {
          INSERT_INFO(TABLE_NAME);
          INTO_COLUMNS(String.join(", ", ALL_COLUMNS));
          INTO_VALUES(ALL_COLUMNS.stream()
              .map(column -> "#{" + StringCaseConverter.snakeToCamel(column) + "}")
              .collect(Collectors.joining(", ")));
        }
      }.toString();
    }

    public static String deleteOld(int maxCount) {
      return new SQL() {
        {
          DELETE_FROM(TABLE_NAME);
          WHERE("time < (SELECT time FROM" + TABLE_NAME
              + " ORDER BY time DESC LIMIT 1 OFFSET #{maxCount}-1");
        }
      }.toString();
    }

  }
}

```


```java
class Test {

  @TestInstance(Lifecycle.PER_CALSS)
  @Nested
  @Order(10)
  @TestMethodOrder(OrderAnnotation.class)
  class DeleteOldEventTest {
    private final OffsetDateTime odt4 = OffsetDateTime.of(2022, 7, 10, 12, 30, 50, 0, ZoneOffset.UTC);
    private final OffsetDateTime odt5 = OffsetDateTime.of(2022, 7, 11, 14, 20, 35, 0, ZoneOffset.UTC);
  
    private final Event orgEv4 = createEvent("Critical", odt4, "28.3", "dummy_message1");
    private final Event orgEv5 = createEvent("Warning", odt4, "13.1", "dummy_message2");

    @BeforeAll
    void beforeAll() {
      repository.save(orgEv4);
      repository.save(orgEv5);
    }

    @PrameterizedTest
    @CsvSource({"5", "6"})
    @Order(11)
    void deleteOld_when_more_than_total_count(int maxCount) {
      assertThat(repository.totalCount()).isEqualTo(5);
      assertThatException().isThrownBy(() -> repository.deleteOld(maxCpount));
      assertThat(repository.totalCount()).isEqualTo(5);
    }

    @Test
    void deleteOld() {
      assertThat(repository.totalCount()).isEqualTo(5);
      assertThatNoException().isThrowBy(() -> repository.deleteOld(3));
      assertThat(repository.totalCount()).isEqualTo(3);
      List<Event> events = repository.list(EventFilter.builder().build(), null);
      assertThat(event.stream().sorted(Comparator.comparing(e -> e.getTime().getValue().toEpochSecond()))
         .map(e -> e.getTime.getValue()).containsExactly(odt4, odt5)
    }

  }
}

```