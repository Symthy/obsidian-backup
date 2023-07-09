
ref:  [go言語で自分のlocalIPを取得する](https://sumito.jp/2019/07/03/go%E8%A8%80%E8%AA%9E%E3%81%A7%E8%87%AA%E5%88%86%E3%81%AElocalip%E3%82%92%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B/)

net.InterfaceAddrs() で取得

```go
package main

import (
	"fmt"
	"net"
)

func main() {
	netInterfaceAddresses, _ := net.InterfaceAddrs()
	for _, netInterfaceAddress := range netInterfaceAddresses {
		networkIp, ok := netInterfaceAddress.(*net.IPNet)
		if ok && !networkIp.IP.IsLoopback() && networkIp.IP.To4() != nil {
			ip := networkIp.IP.String()
			fmt.Println("Resolved Host IP: " + ip)
		}
	}
}
```