
## useState 地獄の治療法

- useReducer を使って、ひとまとめにする
- useStateでもひとまとめにできるが、以下のようにstateと有効性を保障のためのロジックもセットにすれば、不正なStateを防げる（UIにもバリデーションは必要、これはORMと同様と考える）
- その前に責務の分離を考えた方がよさそう

```typescript
import { useReducer } from "react";

function EditCalendarEvent() {
  const [event, updateEvent] = useReducer(
    (prev, next) => {
      const newEvent = { ...prev, ...next };

      // Ensure that the start date is never after the end date
      if (newEvent.startDate > newEvent.endDate) {
        newEvent.endDate = newEvent.startDate;
      }

      // Ensure that the title is never more than 100 chars
      if (newEvent.title.length > 100) {
        newEvent.title = newEvent.title.substring(0, 100);
      }
      return newEvent;
    },
    { title: "", description: "", attendees: [] }
  );

  return (
    <>
      <input
        value={event.title}
        onChange={(e) => updateEvent({ title: e.target.value })}
      />
      {/* ... */}
    </>
  );
}
```


ref : [A cure for React useState hell?](https://dev.to/builderio/a-cure-for-react-usestate-hell-1ldi)