# pflag包的基本使用

pflag包是基于golang flag包的一个扩展，除了和flag一样提供基础类型的支持外，pflag提供了更加精细的类型还有诸如ip、ipmask、ipnet等类型和其他基础类型切片的支持。

另外pflag支持Shorthand(flag 缩写)、Deprecated(flag 废弃)、Hidden(flag 隐藏)等功能

## 主要接口

- **Value**

pflag中的Value是一个实现了Value接口的变量,任意类型只要实现了这三个方法即可作为Flag中的Value

```go
type Value interface {
	String() string
	Set(string) error
	Type() string
}
```

- **SliceValue**

  pflag中支持切片类型，切片类型除了实现了Value接口外 还都实现了SliceVaule接口

```go
type SliceValue interface {
	// Append adds the specified value to the end of the flag value list.
	Append(string) error
	// Replace will fully overwrite any data currently in the flag value list.
	Replace([]string) error
	// GetSlice returns the flag value list as an array of strings.
	GetSlice() []string
}
```



## 主要结构体

- Flag 结构体

  pfalg中的flag结构体描述了一个flag的基本信息，包括相较于flag包中支持Shorthand、Deprecated、Hidden的字段

  ```go
  type Flag struct {
    Name                string              // Flag名称
    Shorthand           string              // Flag的缩写
  	Usage               string              // 帮助信息
  	Value               Value               // 实现Vaule接口的变量
  	DefValue            string              // default value (as text); for usage message
  	Changed             bool                // flag是否设值
  	NoOptDefVal         string              // 无选项时默认值
  	Deprecated          string              // Flag废弃标志
  	Hidden              bool                // Flag是否隐藏
  	ShorthandDeprecated string              // Flag缩写废弃标志
  	Annotations         map[string][]string // used by cobra.Command bash autocomple code
  }
  ```

- FlagSet 结构体

  FlagSet是一个描述Flag的结构体，并提供解析各类flag的方法。

  ```go
  type FlagSet struct {
  	
  	Usage func() //解析FlagSet出错时的打印函数,可由用户自己定义
  
  	// SortFlags is used to indicate, if user wants to have sorted flags in
  	// help/usage messages.
  	SortFlags bool
  
  	// ParseErrorsWhitelist is used to configure a whitelist of errors
  	ParseErrorsWhitelist ParseErrorsWhitelist
  
  	name              string
  	parsed            bool
  	actual            map[NormalizedName]*Flag //
  	orderedActual     []*Flag
  	sortedActual      []*Flag
  	formal            map[NormalizedName]*Flag 
  	orderedFormal     []*Flag
  	sortedFormal      []*Flag
  	shorthands        map[byte]*Flag
  	args              []string // arguments after flags
  	argsLenAtDash     int      // len(args) when a '--' was located when parsing, or -1 if no --
  	errorHandling     ErrorHandling
  	output            io.Writer // nil means stderr; use out() accessor
  	interspersed      bool      // allow interspersed option/non-option args
  	normalizeNameFunc func(f *FlagSet, name string) NormalizedName
  
  	addedGoFlagSets []*goflag.FlagSet
  }
  ```

  ## Demo样例

  pflag中定义flag的方式一般是用包级的**对外函数**，也就是使用全局变量**CommandLine**这个**FlagSet**

  ```go
  // CommandLine is the default set of command-line flags, parsed from os.Args.
  var CommandLine = NewFlagSet(os.Args[0], ExitOnError)
  ```

  当然你也可自定义自己的FlagSet来实现其它的需求，比如定义**errorHandling**来决定如何处理错误。

  比如

  ```go
  myFlagSet:=pflag.NewFlagSet(os.Args[0],flag.PanicOnError)
  ```

  

  CommandLine使用的是ExitOnError，它的处理方式是出错时退出程序。

  pflag包定义了三种出错处理类型

  ```go
  type ErrorHandling int
  
  const (
  	// ContinueOnError will return an err from Parse() if an error is found
  	ContinueOnError ErrorHandling = iota
  	// ExitOnError will call os.Exit(2) if an error is found when parsing
  	ExitOnError
  	// PanicOnError will panic() if an error is found when parsing flags
  	PanicOnError
  )s
  ```

  ```go
  func (f *FlagSet) Parse(arguments []string) error {
  	if f.addedGoFlagSets != nil {
  		for _, goFlagSet := range f.addedGoFlagSets {
  			goFlagSet.Parse(nil)
  		}
  	}
  	f.parsed = true
  
  	if len(arguments) < 0 {
  		return nil
  	}
  
  	f.args = make([]string, 0, len(arguments))
  
  	set := func(flag *Flag, value string) error {
  		return f.Set(flag.Name, value)
  	}
  
  	err := f.parseArgs(arguments, set)
    //此处逻辑实现了这三种错误的处理
  	if err != nil {
  		switch f.errorHandling {
  		case ContinueOnError:
  			return err
  		case ExitOnError:
  			fmt.Println(err)
  			os.Exit(2)
  		case PanicOnError:
  			panic(err)
  		}
  	}
  	return nil
  }
  ```

  

  **几种定义flag方式**

  ```go
  //返回存储flag值的指针
  func String(name string, value string, usage string) *string {
  	return CommandLine.StringP(name, "", value, usage)
  }
  //定义shorthand,返回存储flag值的指针
  func StringP(name, shorthand string, value string, usage string) *string {
  	return CommandLine.StringP(name, shorthand, value, usage)
  }
  // 绑定已有变量，并提供默认值value
  func StringVar(p *string, name string, value string, usage string) {
  	CommandLine.VarP(newStringValue(value, p), name, "", usage)
  }
  //绑定已有变量，提供默认值value，同时定义shorthand
  func StringVarP(p *string, name, shorthand string, value string, usage string) {
  	CommandLine.VarP(newStringValue(value, p), name, shorthand, usage)
  }
  
  ```

  **example**

  ```go
  author:=pflag.StringP("author","u","anonymous","software author")//返回string类型的指针
  ```

  ```go
  var author string
  pflag.StringVarP(&author,"u","anonymous","software author")//绑定已有变量
  ```

  **不管是用全局变量CommandLine定义flag还是用自定义FlagSet来定义flag,最终都是用统一的使用Value接口的方法来完成**

  1. **StringP调用StringVarP**

  ```go
  func (f *FlagSet) StringP(name, shorthand string, value string, usage string) *string {
  	p := new(string)
  	f.StringVarP(p, name, shorthand, value, usage)
  	return p
  }
  ```

  2. **StringVarP调用VarP **

  **注意：在这里newStringValue则把各种类型转换为Value接口类型 ，在下一步中实现了统一处理**

  ```go
  func (f *FlagSet) StringVarP(p *string, name, shorthand string, value string, usage string) {
  	f.VarP(newStringValue(value, p), name, shorthand, usage)//
  }
  ```

  3. **VarP调用VarPF VarPF最终利用这些参数生成flag 并添加到FlagSet中**

  ```go
  func (f *FlagSet) VarP(value Value, name, shorthand, usage string) {
  	f.VarPF(value, name, shorthand, usage)
  }
  ```

  ```go
  func (f *FlagSet) VarPF(value Value, name, shorthand, usage string) *Flag {
  	// Remember the default value as a string; it won't change.
  	flag := &Flag{
  		Name:      name,
  		Shorthand: shorthand,
  		Usage:     usage,
  		Value:     value,
  		DefValue:  value.String(),
  	}
  	f.AddFlag(flag)//添加flag到FlagSet中，此时还只是添加到FlagSet中的formal和orderedFormal中，只有调用Parse的时候才会添加到actual这个map中
  	return flag
  }
  ```

  

  