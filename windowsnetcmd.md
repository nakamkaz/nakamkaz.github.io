# netshでIPアドレスを与える

## インターフェースのIdxを確認する
````cmd
netsh int ipv4 show int
````

````
Idx  ... Met ...  MTU ... 状態 ... 名前
3        25      1500    Connect   アダプタ名前
````

Idx 3であるとする

````cmd
netsh int ipv4 set address 3 static 192.168.15.3 255.255.255.0 192.168.14.1 1
````
  Idx source  ipv4addr netmask gwaddr met

## 名前解決の確認テスト  
````powershell
Resolve-DNSName webserverfqdn  
 
Resolve-DNSName webserverfqdn [-Server DNSserver]
````
