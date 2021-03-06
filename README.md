"# simple-excelize" 

##  一、基于xml模板创建excel文件

```go
package main

import (
    "github.com/bluebuff/simple-excelize/core"
	"github.com/bluebuff/simple-excelize/xml"
	"io/ioutil"
	"time"
)


func main() {
    engine := xml.Open("./config/student.xml")
    studentDataList := NewStudentDataList()
    builder := core.NewExcelBuilder()
    builder.RegisterStyle()
    handlers, err := engine.Schema("student-list").Scan(studentDataList)
    if err != nil {
        panic(err)
    }
    builder.JoinSheet("list", handlers...)
    bytes, err := builder.Build()
    if err != nil {
        panic(err)
    }
    err = ioutil.WriteFile("./demo.xlsx", bytes, 0666)
    if err != nil {
        panic(err)
    }
}

func NewStudentDataList() interface{} {
	// return data
    return nil
}

```

## 二、手动创建

```go

package main

import (
    "fmt"
	"github.com/bluebuff/simple-excelize/core"
	"github.com/bluebuff/simple-excelize/core/context"
    "github.com/bluebuff/simple-excelize/models"
	"io/ioutil"
)

func main() {
    builder := core.NewExcelBuilder()
    builder.RegisterStyle()
    builder.JoinSheet("列表1", beforeHandle, theadHandler, tbodyHandler, tfootHandler, afterHandler)
   	builder.JoinSheet("列表2", buildSheetFunc, afterHandler)
    builder.Active("列表1")
    bytes, err := builder.Build()
    if err != nil {
       fmt.Println(err)
       return
    }
    err = ioutil.WriteFile("./student-info.xlsx", bytes, 0666)
    if err != nil {
         fmt.Println(err)
         return
    }
}

func beforeHandle(ctx context.Context) error {
	ctx.SetLayout(&models.Layout{
		Left:  2,
		Top:   2,
		Right: 5,
	})
	ctx.SetColWidth(1, 2, 50)
	return nil
}

func afterHandler(ctx context.Context) error {
	fmt.Println(ctx.String())
	return nil
}

func theadHandler(ctx context.Context) error {
	ctx.SetTitleLine("学生信息表")
	ctx.NewLine()
	return nil
}

func tbodyHandler(ctx context.Context) error {
	ctx.SetHeader("姓名")
	ctx.SetHeader("年龄")
	ctx.SetHeader("学分")
	ctx.NewLine()
	ctx.SetString("张三")
	ctx.SetUint32(26)
	ctx.SetFloat32(98)
	firstAxis := ctx.LastAxis()
	ctx.NewLine()
	ctx.SetString("李四")
	ctx.SetUint32(28)
	ctx.SetFloat32(100)
	lastAxis := ctx.LastAxis()
	ctx.NewLine()
	ctx.SetStringLine("")
	ctx.NewLine()
	ctx.SetHeader("小计")
	ctx.SetHeader("")
	ctx.SetFormula(fmt.Sprintf("=SUM(%s:%s)", firstAxis, lastAxis), context.SetConditionStyle(common.SubtotalDecimals, 200))
	ctx.NewLine()
	ctx.SetStringLine("test", false)
	ctx.NewLine()
	return nil
}

func tfootHandler(ctx context.Context) error {
	ctx.SetStringLine("备注:xxxxx", false)
	ctx.NewLine()
	return nil
}

func buildSheetFunc(ctx context.Context) error {
	ctx.SetLayout(&models.Layout{
		Left:  2,
		Top:   2,
		Right: 5,
	})
	ctx.SetColWidth(1, 2, 50)
	// thead
	ctx.SetTitleLine("学生信息表")
	ctx.NewLine()

	// tbody
	ctx.SetHeader("姓名")
	ctx.SetHeader("年龄")
	ctx.SetHeader("学分")
	ctx.NewLine()
	ctx.SetString("张三")
	ctx.SetUint32(26)
	ctx.SetFloat32(98)
	firstAxis := ctx.LastAxis()
	ctx.NewLine()
	ctx.SetString("李四")
	ctx.SetUint32(28)
	ctx.SetFloat32(100)
	lastAxis := ctx.LastAxis()
	ctx.NewLine()
	ctx.SetStringLine("")
	ctx.NewLine()
	ctx.SetHeader("小计")
	ctx.SetHeader("")
	ctx.SetFormula(fmt.Sprintf("=SUM(%s:%s)", firstAxis, lastAxis), context.SetConditionStyle(common.SubtotalDecimals, 200))
	ctx.NewLine()
	ctx.SetStringLine("test", false)
	ctx.NewLine()

	// tfoot
	ctx.SetStringLine("备注:xxxxx", false)
	ctx.NewLine()
	return nil
}


```