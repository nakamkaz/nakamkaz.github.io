#  yyyy.mm.dd 2024.05.28

## mapの作成

map[キーの型名]値の型名{}

````go
mp := map[string]int{}
mp["key1"]=val1
mp["key2"]=val2
````

## 定義と同時に値をいれる
````go
map[string]=int{
"K1":10,
"K2":20,
"K3":33,
// 最後に,をつける
}
````

## mapをmakeで作成する
````go
mp2:=make( map[キーの型名]値の型名 )
````
