
## X.509 証明書内容の表示

````shell
openssl x509 -text -noout -in 入力ファイル
````

## HTTPリクエスト
````shell
openssl s_client -connect webserverhost:443
````
