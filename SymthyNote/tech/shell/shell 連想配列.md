
- 
```sh
declare -A KEY_TO_STRS
KEY_TO_VALUES["key1"]="value1-1 value1-2"
KEY_TO_VALUES["key2"]="value2-1 value2-2"

for key in "${!KEY_TO_VALUES[@]}" do
  local readonly value_str=${KEY_TO_VALUES[${key}]}
  local readonly values=(${value_str[@]})
  local readonly value_one=${values[0]}
done

```

#shell