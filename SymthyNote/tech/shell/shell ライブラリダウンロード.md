

```sh
# lua-resty-jwt-0.2.2
curl -# -LJO -H 'Accept: application/octet-stream' "https://luarocks.org/manifests/cdbattags/"
```

```shell
readonly KONG_VERSION="3.1.1"
readonly rpm_file_name="kong-${KONG_VERSION}.rhel7.9.amd64.rpm"
readonly repository_url="https://download.konghq.com/gateway-3.x-rhel-7/Packages/k/"
```


```shell

function downloadOSSWithVersionCheck() {
  local readonly check_latest=$1
  local readonly repository_url="$2"
  local readonly rpm_file_name="$3"
  local readonly search_rpm_string="$4"
  echo "[INFO] Downloading ${rpm_file_name} ..." >&2
  if [ "${check_latest}" -eq ${OSS_CHECK_LATEST} ]; then
    local latest_version=
    local readonly awk_cmd="awk 'match(\$0, />${search_rpm_string}</) {print substr(\$0, RSTART+1, RLENGTH-2)}' | sort --version-sort | tail -n 1"
    latest_version=$(curl -sk "${repository_url}" | eval ${awk_cmd} | tail -n 1)
    if [ "${rpm_file_name}" = "${latest_version}" ]; then
      echo "[INFO] ${rpm_file_name} is the latest version."
    else
      echo "[WARN] ${rpm_file_name} is the latest version, ${latest_version} is the latest version."
    fi
  fi

  curl -# -LJO -H 'Accept: application/octet-stream' "${repository_url}${rpm_file_name}" 1>%2
  checkMD5ofRpm ${rpm_file_name}
}

function checkMD5ofRpm() {
  local readonly rpm_file_name=$1
  echo "[INFO] Checking MD5 of ${rpm_file_name} ..." >&2
  rpm --checksig --nosignature ${rpm_file_name} 1>&2
  if [ $? -ne 0 ]; then
    echo "[ERROR] rpm file is invalid. (${rpm_file_name})"
    exit 1
  fi
}
```


#shell 