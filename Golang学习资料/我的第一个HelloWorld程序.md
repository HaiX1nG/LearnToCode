# :palm_tree:我的第一个HelloWorld程序



## :deciduous_tree: 1. 创建第一个.go文件

1. 打开Goland点击`NewProject`创建一个新项目

2. 创建一个`GO file`

3. 在IDE里输入:

    ```go
    package main
    
    import "fmt"
    
    func main() {
    	fmt.Println("Hello World")
    }
    ```

    ```powershell
    GOROOT=D:\Golang1.16.15\Go #gosetup
    GOPATH=D:\Golang1.16.15\GoWorks\src;D:\Golang1.16.15\GoWorks #gosetup
    D:\Golang1.16.15\Go\bin\go.exe build -o C:\Users\14654\AppData\Local\JetBrains\GoLand2023.2\tmp\GoLand\___go_build_awsomeProject.exe awsomeProject #gosetup
    C:\Users\14654\AppData\Local\JetBrains\GoLand2023.2\tmp\GoLand\___go_build_awsomeProject.exe
    Hello World
    
    Process finished with the exit code 0
    ```

    

    __这样我们的Hello World!打印成功了,我们也顺利的完成了我们的第一个HelloWorld程序__