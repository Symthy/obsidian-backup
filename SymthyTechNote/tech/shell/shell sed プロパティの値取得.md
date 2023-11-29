
```sh
function get_property_value() {
  local property_file_path=$1
  local property_key=$2
  local _exp=${$2//./\\.}

  if [ -f "${property_file_path}" ]; then
    sed -n "/^${_exp}\s*=/s/^[^=]*\s*=\s*//p" "${property_file_path}"
  fi
}
```