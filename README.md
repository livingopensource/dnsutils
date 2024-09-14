[![Go Report Card](https://goreportcard.com/badge/github.com/livingopensource/dnsutils)](https://goreportcard.com/report/livingopensource/dnsutils)
[![](https://godoc.org/github.com/livingopensource/dnsutil?status.svg)](https://godoc.org/github.com/livingopensource/dnsutils)

# dnsutils
#### Golang DNS  dig

```sh
go get github.com/livingopensource/dnsutils
```


```go
//Normal

package main

import (
	"fmt"
	"github.com/livingopensource/dnsutils"
)

func main() {
    var dig dnsutil.Dig 
    dig.SetDNS("8.8.8.8") //or ns.xxx.com 
    a, err := dig.A("google.com")  // dig google.com @8.8.8.8
    fmt.Println(a, err)
}
```

```go
//msg

package main

import (
	"fmt"
	"github.com/livingopensource/dnsutils"
	"github.com/miekg/dns"
)

func main() {
	var dig dnsutil.Dig
	dig.SetDNS("1.1.1.1")
	msg, err := dig.GetMsg(dns.TypeA, "google.com")
	if err != nil {
		fmt.Println(err)
		return
	}
	// fmt.Println(msg)
	// or
	fmt.Println(msg.Question) //question section
	fmt.Println(msg.Answer)   //answer section.
	fmt.Println(msg.Ns)       //authority section.
	fmt.Println(msg.Extra)    //additional section.
}

```


```go
//EDNS0ClientSubnet

package main

import (
	"fmt"
	"github.com/livingopensource/dnsutils"
)

func main() {
    var dig dnsutil.Dig
    dig.SetDNS("8.8.8.8") //or ns.xxx.com
    dig.SetEDNS0ClientSubnet("123.123.123.123")   //support edns0clientsubnet
    a, err := dig.A("google.com")  // dig google.com @8.8.8.8 +client=123.123.123.123
    fmt.Println(a, err)
}
```


```go
//Retry

package main

import (
	"fmt"
	"github.com/livingopensource/dnsutils"
)

func main() {
    var dig dnsutil.Dig
    dig.Retry=3 //retry  when write or read message return error . defualt 1
    dig.SetDNS("8.8.8.8") //or ns.xxx.com 
    a, err := dig.A("google.com")  // dig google.com @8.8.8.8
    fmt.Println(a, err)
}
```


```go
//dig +trace

package main

import (
	"fmt"
	"github.com/livingopensource/dnsutils"
)

func main() {
	domain := "google.com"
	var dig dnsutil.Dig
	rsps, err := dig.Trace(domain)  //dig +trace google.com
	if err != nil {
		fmt.Println(err)
		return
	}
	for _, rsp := range rsps {
		if rsp.Msg.Authoritative {
			for _, answer := range rsp.Msg.Answer {
				fmt.Println(answer)
			}
		}
		for _, ns := range rsp.Msg.Ns {
			fmt.Println(ns)
		}
		fmt.Println("\tReceived from", rsp.Server, rsp.ServerIP)
	}
}
```


```go
// Check if DNS is polluted

package main

import (
	"fmt"
	"github.com/livingopensource/dnsutils"
)

func main() {
	polluted, err := dnsutil.IsPolluted("facebook.com")
	if err != nil {
		fmt.Println(err)
		return
	}
	if polluted {
		fmt.Println("DNS is polluted")
	} else {
		fmt.Println("Ok")
	}
}
```


```go
//SetBackupDNS
//Initiate requests to two DNS settings at the same time, returning the fastest response result
package main

import (
	"fmt"
	"github.com/livingopensource/dnsutils"
)

func main() {
	var dig dnsutil.Dig
	dig.SetDNS("1.1.1.1")
	dig.SetBackupDNS("8.8.8.8")
	a, err := dig.A("google.com")
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(a)
}

```