#### 代码生成
**原始结构**
在$GOPATH/src下建立k8s-controller-custom-resource项目,项目结构如下:

```sh
.
├── controller.go
├── crd
│   └── network.yaml
├── example
│   └── example-network.yaml
├── go.mod
├── go.sum
├── main.go
└── pkg
    └── apis
        └── samplecrd
            ├── register.go
            └── v1
                ├── doc.go
                ├── register.go
                └── types.go

```

samplecrd是组名,v1是版本。
注释解析:

> // +k8s:deepcopy-gen=package   ==>为保利的所有类型生成DeepCopy方法
>
> // +groupName=samplecrd.my.crd ==>指定API组名
>
> // +genclient  ==> 为类型生成客户端代码
> // +genclient:noStatus ==>这个API资源类型定义里没有Status字段,不加生成Clent会自动带上UpdateStatus方法
> // +k8s:deepcopy-gen:interfaces=k8s.io/apimachinery/pkg/runtime.Object   ==>在生成DeepCopy时实现k8s提供的runtime.Object接口

代码生成:

在项目目录下创建hack目录,并在hack目录下创建如下文件:
boilerplate.go.txt
tools.go
update-codegen.sh


在hack目录下执行如下命令生成代码:

```sh
go mod tidy
go mod download
# 把k8s.io/code-generator依赖导入项目目录
go mod vendor
# 执行代码生成命令
./update-codegen.sh
```

生成代码完成后的项目结构如下所示:
生成代码完成后的项目结构如下所示:

```sh
.
├── controller.go
├── crd
│   └── network.yaml
├── example
│   └── example-network.yaml
├── go.mod
├── go.sum
├── hack
│   ├── boilerplate.go.txt
│   ├── tools.go
│   └── update-codegen.sh
├── main.go
└── pkg
    ├── apis
    │   └── samplecrd
    │       ├── register.go
    │       └── v1
    │           ├── doc.go
    │           ├── register.go
    │           ├── types.go
    │           └── zz_generated.deepcopy.go
    └── client
        ├── clientset
        │   └── versioned
        │       ├── clientset.go
        │       ├── doc.go
        │       ├── fake
        │       │   ├── clientset_generated.go
        │       │   ├── doc.go
        │       │   └── register.go
        │       ├── scheme
        │       │   ├── doc.go
        │       │   └── register.go
        │       └── typed
        │           └── samplecrd
        │               └── v1
        │                   ├── doc.go
        │                   ├── fake
        │                   │   ├── doc.go
        │                   │   ├── fake_network.go
        │                   │   └── fake_samplecrd_client.go
        │                   ├── generated_expansion.go
        │                   ├── network.go
        │                   └── samplecrd_client.go
        ├── informers
        │   └── externalversions
        │       ├── factory.go
        │       ├── generic.go
        │       ├── internalinterfaces
        │       │   └── factory_interfaces.go
        │       └── samplecrd
        │           ├── interface.go
        │           └── v1
        │               ├── interface.go
        │               └── network.go
        └── listers
            └── samplecrd
                └── v1
                    ├── expansion_generated.go
                    └── network.go
```

