#  编译器和链接器

程序代码本质上还是文本(text)，将文本转换为程序需要两个关键操作，**编译和链接**

**源文件(Source file)->编译(Compiling)->链接(Linking)->可执行文件**

## 编译器

编译器做的事情就是**将源代码文本转换成汇编语言，再将汇编语言转换成机器可以识别的机器码**。

每个文件被编译成一个独立的`obj`文件作为`translation unit`

编译器将源代码的文本转换为中继`obj(object)`目标文件，该文件内容就是机器码，然后这些文件会传入链接器(Linker)。

- 编译器工作流程
  - 预处理(pre-process)：处理所有预处理语句
  - 标记解释(tokenizing)和解析(parsing)：将C++文本处理成编译器能处理的语言，创建抽象语法树(abstract syntax tree)，就是代码的表达。简单说就是把代码转化成常数资料(constant data)或者指令(instructions)
  - 生成机器码：将代码转化成cpu可以执行的机器码

示例：

新建一个`math.cpp`文件，写一个简单的乘法函数：

```c++
int Multiply(int a, int b)
{
	int result = a * b;
	return result;
}
```

然后**仅编译这个文件**，会在文件夹中发现出现了`math.obj`文件：


![image-20230801152557005](https://github.com/han-0111/CppLearning/assets/75550897/704da5e6-9038-4ac1-9776-48ec3cd502b3)

同时注意到只有3KB，但是之前的`HelloWorld.obj`和`Log.obj`文件都很大，尽管他们只是打印输出。这是因为这两个文件中都`include`了`iostream`，在这个头文件中包含了很多内容，所以**编译时先执行预处理，此时也会把预处理中的文件一起编译进`obj`目标文件中**。

**那么`obj`里面是什么内容呢？**

这文件里面就是机器可以理解的机器码，我们看不明白。但是可以使用VS中`汇编程序输出FA选项生成math.asm`文件来查看编译过程中的汇编程序：

![image-20230801165354161](https://github.com/han-0111/CppLearning/assets/75550897/928ffece-db78-4aa3-bc66-242c1a1ff2e6)

编译后打开`math.asm`就可以看到代码对应的汇编语言：

```asm
; Listing generated by Microsoft (R) Optimizing Compiler Version 19.35.32215.0 

	TITLE	D:\Cpp\HelloWorld\HelloWorld\Debug\math.obj
	.686P
	.XMM
	include listing.inc
	.model	flat

INCLUDELIB MSVCRTD
INCLUDELIB OLDNAMES

msvcjmc	SEGMENT
__CDAE11DE_math@cpp DB 01H
msvcjmc	ENDS
PUBLIC	?Multiply@@YAHHH@Z				; Multiply
PUBLIC	__JustMyCode_Default
EXTRN	@__CheckForDebuggerJustMyCode@4:PROC
; Function compile flags: /Odt
;	COMDAT __JustMyCode_Default
_TEXT	SEGMENT
__JustMyCode_Default PROC				; COMDAT
	push	ebp
	mov	ebp, esp
	pop	ebp
	ret	0
__JustMyCode_Default ENDP
_TEXT	ENDS
; Function compile flags: /Ogtp
;	COMDAT ?Multiply@@YAHHH@Z
_TEXT	SEGMENT
_a$ = 8							; size = 4
_b$ = 12						; size = 4
?Multiply@@YAHHH@Z PROC					; Multiply, COMDAT
; File D:\Cpp\HelloWorld\HelloWorld\math.cpp
; Line 2
	push	ebp
	mov	ebp, esp
	mov	ecx, OFFSET __CDAE11DE_math@cpp
	call	@__CheckForDebuggerJustMyCode@4
	mov	eax, DWORD PTR _a$[ebp]
	imul	eax, DWORD PTR _b$[ebp]
; Line 5
	pop	ebp
	ret	0
?Multiply@@YAHHH@Z ENDP					; Multiply
_TEXT	ENDS
END

```

可以通过上面的汇编代码看到自定义的函数`?Multiply@@YAHHH@Z`，但是函数在这里会被装饰成带有随机字符和`@`符号的形式，这就是**函数签名**，**用来独一无二的标记该函数，链接器(linker)就是通过这个函数签名来找到函数的。**

**总结来说，编译器就是将源代码文件生成包含机器码和我们定义的常量数据的目标文件object file**

### 预处理(Preprocessing)

预处理阶段编译器检查所有的预处理语句（由`#`符号标记的语句），常见的预处理指令有：

- include
- define
- if 

#### include

`include`：去寻找一个文件，然后将该文件的内容拷贝到当前文件中，这种文件通常被称为**头文件**。

示例：

```c++
int Multiply(int a, int b)
{
	int result = a * b;
	return result  // 明显这里少一个分号
}
```

此时编译提醒缺少`;`。

![image-20230801154509207](https://github.com/han-0111/CppLearning/assets/75550897/6f5a31cd-70f5-4289-9779-6d580d963dbf)

写一个头文件`semicolon.h`

```c++
;
```

这个头文件里只有一个`;`。

既然`include`是把文件拷贝过来，那么现在这个文件里就只有一个`;`，乘法函数里缺少一个`;`，为了验证`include`是拷贝内容，可以在少分号的位置使用`#include "semicolon.h"`试一下：

```c++
int Multiply(int a, int b)
{
	int result = a * b;
	return result
#include "semicolon.h"
}
```

此时编译就可以完全通过了。

![image-20230801154435138](https://github.com/han-0111/CppLearning/assets/75550897/9cef88cf-e1da-4cea-be9d-caf4681856f8)


那么`include`确实是复制文件的内容到当前位置。

#### define

`define`：后面跟两个参数，使用第二个参数替换第一个参数。简单说就是把第一个参数的实际意义替换成后面的参数

示例:

```c++
#define INTEGER int

INTEGER Multiply(int a, int b)  // INTEGER由于在预处理中重新定义成了int, 所以和使用int效果一致
{
	int result = a * b;
	return result;
}
```

这段代码中的预处理语句就是：把代码中出现的`INTEGER`字符替换成`int `字符，效果和直接使用`int`是一样的。

#### if/endif

**`if`预处理语句可以依据特定条件包含或者剔除代码。**

示例：

```c++
#if 0
int Multiply(int a, int b)
{
	int result = a * b;
	return result;
}
#endif // 0
```

此时明显判定条件为`false`，所以编译器是不会编译中间的代码的

预处理结果如下：

```idl
#line 1 "D:\\Cpp\\HelloWorld\\HelloWorld\\math.cpp"






#line 8 "D:\\Cpp\\HelloWorld\\HelloWorld\\math.cpp"


```



当把判定条件改成`true`（1）时：

```c++
#if 1
int Multiply(int a, int b)
{
	int result = a * b;
	return result;
}
#endif // 1

```

此时的预处理结果如下：

```idl
#line 1 "D:\\Cpp\\HelloWorld\\HelloWorld\\math.cpp"

int Multiply(int a, int b)
{
	int result = a * b;
	return result;
}
#line 8 "D:\\Cpp\\HelloWorld\\HelloWorld\\math.cpp"


```

可以看到当判定条件成立时，编译器会预处理`#if`和`endif`所包含的内容；判定条件不成立时，编译器会直接忽略中间的内容。

## 链接器

链接(Linking)的主要工作就是找到**每个符号和函数的位置，并把它们链接在一起。**

链接器的功能：

- 在单个代码文件中，链接器用来找到函数的位置，包括主函数的位置。
- 在包含多个代码文件的项目中，链接器用来将这些文件链接到一个程序。

示例：

```c++
/*没有main函数*/
#include<iostream>

void Log(const char* message)
{
	std::cout << message << std::endl;

}
int Multiply(int a, int b)
{
	Log("Multiply");
	return a * b;
}
```

如果此时仅编译代码，是没有问题的：

![image-20230801181126096](https://github.com/han-0111/CppLearning/assets/75550897/4b6fd5f1-656a-40ba-b2a5-175c2a836c2c)


但是如果进行生成(Build)，会提示链接错误：


![image-20230801181248727](https://github.com/han-0111/CppLearning/assets/75550897/f9731289-4dee-48b8-a664-4620a114ab9a)


>  链接错误的提示就是`LNK`。

我们需要给这个文件一个`main`函数：

```c++
#include<iostream>

void Log(const char* message);

int Multiply(int a, int b)
{
	Log("Multiply");
	return a * b;
}

int main()
{
	std::cout << Multiply(2, 4) << std::endl;
	std::cin.get();
}
```

**比较特殊的一点：**在`main`函数调用了该文件的`Multiply`函数，`Multiply`函数又调用了`Log`函数，链接器去找这个函数，就会在`Log.cpp`文件中找到这个函数，链接成功。

**但是**！！！如果在`main`函数中没有调用`Multiply`函数：

```c++
#include<iostream>

void Log(const char* message);  // 声明有一个函数Log

int Multiply(int a, int b)
{
	Log("Multiply");
	return a * b;
}

int main()
{
	// std::cout << Multiply(2, 4) << std::endl;
	std::cin.get();
}
```

**虽然在主函数中没有调用`Multiply`函数，自然就没有调用`Log`，可是仍然会引发链接错误！！！！！！！！！**

因为链接器会这样认为：这个`Multiply`函数在这个文件中没有被调用，ok，可是其他文件仍然有可能会使用，那我还是要链接它的，等等，没有`Log`啊，给这小子报个错误，快去改！

> visual studio 2022貌似修改了这个错误，测试了下并不会报错

怎么改呢？

在函数类型前加一个关键字`static`，表明这个函数只在这个translation unit里面使用就可以了。

**相关注意事项：**

- **声明的函数的类型，参数类型和数量必须和实际定义的函数保持一致**。
- **避免函数名重复，如果两个函数名字一样，链接器会不知道链接哪一个。**
- **同一个文件中不要出现已经声明了一个函数，又定义了一个同名的函数，即使这个函数功能与其他文件中的函数功能不一致。同样会引发链接错误。**

## 一种比较复杂的情况

现在有一个头文件`Log.h`：

```c++
void Log(const char* message)
{
	std::cout << message << std::endl;
}
```

一个`Log.cpp`：

```c++
#include<iostream>
#include"Log.h"

void InitLog()
{
	Log("Initialized Log");
}
```

一个`Math.cpp`：

```c++
#include<iostream>
#include"Log.h"

static int Multiply(int a, int b)
{
	Log("Multiply");
	return a * b;
}

int main()
{
	std::cout << Multiply(2, 4) << std::endl;
	std::cin.get();
}
```

这种情况是会引发链接错误的：

![image-20230801205300358](https://github.com/han-0111/CppLearning/assets/75550897/c077542c-d76a-4a88-9262-dc76ea1bc497)


因为`#include`预处理的作用就是把包含的文件内容拷贝过来，所以此时`Log.cpp`就等同于：

```c++
#include<iostream>
void Log(const char* message)
{
	std::cout << message << std::endl;
}

void InitLog()
{
	Log("Initialized Log");
}
```

`Math.cpp`就等同于：

```c++
#include<iostream>
void Log(const char* message)
{
	std::cout << message << std::endl;
}

static int Multiply(int a, int b)
{
	Log("Multiply");
	return a * b;
}

int main()
{
	std::cout << Multiply(2, 4) << std::endl;
	std::cin.get();
}
```

在运行`main`函数时，调用`Multiply`函数，函数中又调用`Log`函数，但是此时就相当于在`Math.cpp`有一个`Log`函数，在`Log.cpp`也有一个`Log`函数，虽然是一样的内容，但是链接器会不知道到底该链接哪一个，这种情况就是上面说的有了重名的函数，链接器无法辨别链接哪一个。

**解决方法：**

1. **在定义`Log`函数时使用`static`限定**，

   ```c++
   static void Log(const char* message)
   {
   	std::cout << message << std::endl;
   }
   ```

   这样即使被`include`到了多个文件，那么在每个文件中所复制过去的函数都是被限制在本文件中的，就不存在多个文件中重名的函数的问题了，即使重名，其工作范围也只是函数所在文件了。

2. 使用关键字`inline`，这样在调用函数时，只复制函数内部的语句。

   ```c++
   inline void Log(const char* message)
   {
   	std::cout << message << std::endl;
   }
   ```
