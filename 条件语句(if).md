# 条件语句

如果`if`怎么怎么样就怎么怎么样，否则`else`怎么怎么样：

```c++
#include "Log.h"  // Log.h是头文件章节中的内容
#include<iostream>

int main()
{
	int x = 5;
	bool compareResult = x == 5;  // bool类型变量，赋值为0时结果为false，除此之外全为true，此处x == 5为比较运算，此时为正确的, 值就是1, 也就是true
	if (compareResult) {  // if语句的格式，如果参数部分为true则执行{}内的代码，否则直接跳过
		Log("Hello World!");  // 打印输出字符
		std::cin.get();
	}
}
```

重要但是尽量少用，会让代码执行效率变低

`bool`：布尔类型变量，true/false,但实质上只是两种数字：0和非零的任意数字，0代表false，非0代表true

`==`：等于运算符，判断两端的值是否相等，相等返回true，否则返回false

上述代码的汇编语言：

```idl
	int x = 5;
00A41BF3  mov         dword ptr [x],5  
	bool compareResults = x == 5;
00A41BFA  cmp         dword ptr [x],5  
00A41BFE  jne         main+29h (0A41C09h)  
00A41C00  mov         dword ptr [ebp-4Ch],1  
00A41C07  jmp         main+30h (0A41C10h)  
00A41C09  mov         dword ptr [ebp-4Ch],0  
00A41C10  mov         al,byte ptr [ebp-4Ch]  
00A41C13  mov         byte ptr [compareResults],al  
	if (compareResults) {
00A41C16  movzx       eax,byte ptr [compareResults]  
00A41C1A  test        eax,eax  
00A41C1C  je          main+57h (0A41C37h)  
		Log("Hello World!");
00A41C1E  push        offset string "Hello World!" (0A47B30h)  
00A41C23  call        Log (0A4120Dh)  
00A41C28  add         esp,4  
		std::cin.get();
00A41C2B  mov         ecx,dword ptr [__imp_std::cin (0A4A0B0h)]  
00A41C31  call        dword ptr [__imp_std::basic_istream<char,std::char_traits<char> >::get (0A4A0B4h)]  
	}
}
```

`if`语句的参数不一定非要是一个确定的变量，只要是可以返回一个结果的内容，都可以，上面的代码就可以直接写成下面的内容：

```c++
#include "Log.h"  
#include<iostream>

int main()
{
	int x = 5;
	if (x == 5) 
    {
		Log("Hello World!");
		std::cin.get();
	}
}
```

因为`==`运算符会有两种结果：0或者1，而`if`判断语句只要判断括号内的条件是不是0就行了，是0就是false，不执行`if`里面的内容，是其他的值就是true，执行`if`下面的内容。

如果`if`语句只包含一行待执行的代码语句，可以去掉`{}`，：

```c++
#include "Log.h"  
#include<iostream>

int main()
{
	int x = 5;

	if (x == 5) 
		Log("Hello World!");

	std::cin.get();
	
}
```

**`if`只检查数字！！！！！！**

与`if`对应就是`else`：

```c++
#include "Log.h"  
#include<iostream>

int main()
{
	const char* ptr = 0;  // 指针ptr指向空(NULL)
	if (ptr)  
		Log(ptr);  // 判定条件为false, 跳转至else
	else  
		Log("ptr is null");  // 打印输出相应内容
	std::cin.get();
}
```

> 即使`if`语句写成了两行，但实际上仍然是一句代码，判断一条语句的结束就是看`;`，一个分号表示该句结束。

除了上述这样给代码两条分支以外，还可以叠加使用`if`，就是`else if`：

```c++
#include "Log.h"  
#include<iostream>

int main()
{
	const char* ptr = "Hello";  // 指针ptr
	if (ptr)
		Log(ptr);  // 判定条件为true, 进入该分支，打印输出
	else  if (ptr == "Hello")  // 尽管为true,但是并不会执行,因为第一个分支的判定true就会跳出分支结构
		Log("ptr is Hello");  
	else
		Log("ptr is null");
	std::cin.get();
}
```

需要注意，如果代码已经进入了一个分支，那么其他分支是不会执行的，上面代码中的第二个条件是不会执行的，因为如果`ptr == "Hello"`是成立的，那么在第一个条件语句判断时就是成立的，就只会执行第一个分支内的代码，后面的就不会执行了。

**只有前面的分支判断条件为false时，后面的分支判断才会执行。如果第一个分支的条件成立，那么自然执行第一个分支的内容，后面的分支会直接跳过**

如果我们想让中间的分支也执行，可以再重新创建一个分支：

```c++
#include "Log.h"  
#include<iostream>

int main()
{
	const char* ptr = "Hello";  // 指针ptr
	if (ptr)
		Log(ptr);  // 判定条件为true,该分支结束，继续执行分支结构后面的代码
	if (ptr == "Hello")  // 进入第二个分支
		Log("ptr is Hello");  // 打印输出相应内容
	else
		Log("ptr is null");
	std::cin.get();
}
```

这样就会打印两条语句。

也就是说**`if`和`else`是相对应的，上面代码的`else`对应的就是第二个`if`**

**另外`else if`并不是关键字**，它只是`else`和`if`的一种组合，`else`相当于它所对应的`if`的另一条分支，只不过在这一条分支中，首先是一个条件判断而已，所做的事情就是：

```c++
#include "Log.h"  
#include<iostream>

int main()
{
	const char* ptr = "Hello";
	if (ptr)
		Log(ptr);  
	else  
	{
		if (ptr == "Hello")
			Log("ptr is Hello");  
		else
			Log("ptr is null");
	}
	std::cin.get();
}
```

> 虽然分支和比较在一个应用中非常重要，但是比较会降低代码的运行效率，能用其他的方法代替的话就尽量代替掉比较操作
