
RET_OK=0
RET_NG=1
### 数値か判定
```sh
function is_numeric() {
  local target=$1
  expr "${target}" + 1 > /dev/null 2>&1
  if [ $? -lt 2]; then
    return ${RET_OK}
  else 
    return ${RET_NG}
  fi
}
```


### 上限/下限内か

```sh
function is_within_range() {
  local value=$1
  local lower_limit=$2
  local upper_limit=$3

  if [ ${value} -lt ${lower_limit} ]; then
    return ${RET_OK}
  elif [ ${value} -gt ${upper_limit} ]; then
    return ${RET_NG}
  fi
  return ${RET_OK}
}
```

### スーパーユーザか判定

```sh
function is_super_user() {
  if [ ${EUID}:-${UID} -ne 0 ]; then
    return ${RET_NG}
  fi
  return ${RET_OK}
}
```


### OSのメジャーバージョン取得

```sh
readonly SYSTEM_RELEASE="/etc/system-release"
readonly OS_RELEASE="/etc/os-release"

function get_os_major_version() {
  local os_version="unknown"
  if [ -e ${SYSTEM_RELEASE} ]; then
    os_version=`cat ${SYSTEM_RELEASE} | awk -Frelease '{print $2}' | awk '{print $1}' | awk -F. '{print $1}'`
  elif [ -e ${OS_RELEASE} ]; then
    os_version=`cat ${OS_RELEASE} | grep "^VERSION_ID=" | awk -F\" '{print $2}' | awk -F. '{print $1}'`
  fi
  echo "${os_version}"
}
```