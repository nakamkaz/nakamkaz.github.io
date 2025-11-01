
#  golang interfaceを使う

## 目的

Caller( 引数 ) という呼び出しを行える関数を作成したい。

## 条件

この 「引数」 のところには 「特定の関数が実装された型」を置いてそれを呼び出す必要がある。

この「特定の関数が実装された型」とは将来的にいろんな型が作成されて指定されることになるのだが、
呼び出す側として常に Caller( 「特定の関数が実装された型」 ) ができれば良く、新しく作られた型の名前や実装内容に依存した考慮はしたくない。

この場合の実装とはType1やType2にたいして以下のように関数が定義されていることを意味する。

```go
type Type1 struct { ... }
type Type2 struct { ... }

func (a Type1) func1(){ ... } 
func (a Type2) func1(){ ... } 
```

## 考察

Caller( 「特定の関数が実装された（実装が保証された）型」 )  という呼び出しを行えればよいということになる。

引数に指定された型のほうで実装された関数func1()を呼び出したいため、共通点としてfunc1()を実装する必要がある。

上の例ではType1とType2についてはどちらもfunc1()という名前の関数が定義されている必要がある。（実装）

そのうえでType1とType2を共通に扱えるような都合の良い箱が欲しい。そこでinterfaceを使う。

## 例

```go

package main

import (
	"log"
	"fmt"
)


type Type1 struct {
	id   int
	text string
}

type Type2 struct {
	id int
	text string
	mon int
}
func (a *Type1) func1() {
	a.id=a.id*10
	a.text=a.text+"_aaa"
}
func (a *Type2) func1(){
	a.mon=(a.mon+1)*100
}
func (a *Type1) func2() {
	a.text = a.text + "_F2"
}
func (a *Type2) func2(){
	a.text = a.text + "_E2"
	a.mon=(a.mon+1)*100
}

type Infs interface {
/* ここに記述された関数を持つ型を格納する変数はこのインターフェース型変数に格納できる。
*/
	func1()
}
/* 本来の目的。 呼び出し側の関数。 引数にインターフェース型を取る */
func Caller(i Infs){
	fmt.Println(i)
}

func main() {
	log.Println()
	var p,q Infs
	x := Type1{id: 1, text: "abc"}
	log.Println(x)
	y := Type2{id: 2, text: "INSTALL"}
	log.Println(y)
	p=&x   // Infs インターフェース型の変数には func1()を実装した型を入れられる。ここではType1
	p.func1()  // インターフェース型の変数pからfunc1()を呼び出せる。
	q=&y   // Infs インターフェース型の変数には func1()を実装した型を入れられる。ここではType2
	q.func1() // インターフェース型の変数qからfunc1()を呼び出せる。
	y.func2() // インターフェース型の変数qからはfunc2()を呼び出せない。yから呼んでいる。

	Caller(p)  // Type1型 の変数x についてfunc1()が実行されたあとの状態をPrintlnする
	Caller(q)  // Type2型 の変数y についてfunc1()とfunc2()が実行されたあとの状態をPrintlnする
}
```

呼び出す側 Caller(i) は i Infs に格納された型が何者であるかを考慮しなくてよい。
つまり、Type1の類似物がいくら増えても、func1()を持つことを守っていれば、iにはどんな型の変数を入れてもよく、Caller(i)で呼び出せる。

