# Install

**Compatible with go1.7+ **

```sh
$ go get -u github.com/kataras/iris/iris
```

this will update the dependencies also.

* If you are connected to the internet through **China**, according to [this](https://github.com/kataras/iris/issues/98), you might have problems installing Iris.   
  **Follow the below steps**:

1. [https:\/\/github.com\/northbright\/Notes\/blob\/master\/Golang\/china\/get-golang-packages-on-golang-org-in-china.md](https://github.com/northbright/Notes/blob/master/Golang/china/get-golang-packages-on-golang-org-in-china.md) 

1. `$ go get github.com/kataras/iris/iris` **without -u**

* If you have any problems installing Iris, just delete the directory `$GOPATH/src/github.com/kataras/iris` , open your shell and run `go get -u github.com/kataras/iris/iris` .

