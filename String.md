# string

- 表示可变长的字符序列
- 定义在命名空间`std`中

代码示例：

```c++
#include <iostream>
#include <string>

int main()
{
	std::string s;  // 默认初始化 空字符串
	std::cout << s << std::endl;
	std::cout << sizeof(s) << std::endl;  // 28字节
}
```

### 初始化

```c++
#include <iostream>
#include <string>

int main()
{
	std::string s;  // 默认初始化 空字符串
	std::string s1 = "copy";  // 拷贝初始化
	std::string s2("directly");  // 直接初始化
	std::string s3(5, 'h');  // 直接初始化 s3存储的是 5 个 h
    std::cout << s.empty() << std::endl;  // 1 empty()方法返回bool值，为真时表示s是空字符串
}
```

### 注意事项及相关操作

- 输入字符到`string`对象会自动忽略字符串中的空白项，以第一个字符为开始，顺序向后遇到空白结束

```c++
#include <iostream>
#include <string>

int main()
{
	std::string s;  // 默认初始化 空字符串
	std::cin >> s;  // 输入字符串
	std::cout << s << std::endl;  // 从顺序遇到的第一个字符开始,顺序向后第一个空白结束
}
```

![image-20230907233259898](https://github.com/han-0111/CppLearning/assets/75550897/affcd720-26c3-4368-a4ab-e8f8bb30b7af)

采用同样的输入，如果使用两个`string`对象接收的话，每个对象会存储一个连续的字符串：

```c++
#include <iostream>
#include <string>

int main()
{
	std::string s, s1;  // 默认初始化 空字符串
	std::cin >> s >> s1;  // 输入字符串
	std::cout << s << s1 << std::endl;  // 顺序输出两个字符串
}
```

![image-20230907233603622](https://github.com/han-0111/CppLearning/assets/75550897/ad774202-e8b1-4f94-ac61-c4be8020cf8d)



- 字符串的比较

  - 字符串本身有有**长度的不同**，获取字符串的长度可以简单地使用`size()`，注意`sizeof()`是获得对象占用内存空间的大小

    ```c++
    #include <iostream>
    #include <string>
    using namespace std;
    
    int main()
    {
    	string s("cpp");
    	cout << s.size() << endl;  // 3
    	cout << sizeof(s) << endl;  // 28
    }
    ```

  - 字符串之间的**大小不同**，字符串的大小比较：顺序比较对应位置的两个字符，如果字符相同，顺序向后，直到出现不同的字符(包括同一字符的大小写，也属于不同的字符)，字符大的字符串就大，与字符串的长度无关

    ```c++
    #include <iostream>
    #include <string>
    using namespace std;
    
    int main()
    {
    	string s1("abc");  // 长度为3
    	string s2("de");  // 长度为2
    	
    	if (s1 > s2)  // false
    		cout << "s1" << endl;
    	else if (s1 == s2)  // false
    		cout << "s1 == s2" << endl;
    	else  
    		cout << "s2" << endl;  // run s2 大于 s1, 因为 d 大于 a
    }
    ```

- 字符串的相加

  - 字符串可以直接使用`+`运算符相加，效果是左侧的字符串后面拼接右侧的字符串的内容。

    ```c++
    #include <iostream>
    #include <string>
    using namespace std;
    
    int main()
    {
    	string s1("abc");
    	string s2("de");
    	cout << s1 + s2 << endl;  // abcde
    }
    ```

  - 字符串对象可以直接与字符串常量相加，但两个字符串常量是不能直接`+`的。

    ```c++
    #include <iostream>
    #include <string>
    using namespace std;
    
    int main()
    {
    	string s1("abc");
    
    	cout << s1 + "de" << endl;  // abcde
    	cout << "abc" + "de" << endl;  //  error
    }
    ```

- 取出字符串中的字符

  - 字符串是由一个个字符顺序连接组成的，使用c++11 标准中提出的一种语法: 范围for循环可以遍历字符串中的字符

    ```c++
    #include <iostream>
    #include <string>
    using namespace std;
    
    int main()
    {
    	std::string s("abcde");
    	char c;
    	c = s[0];  // 第一个字符"a"
    	cout << "c : " << c << endl;  // c : a
    	for (auto a : s)  // 定义的变量 : 序列对象
    		cout << a << endl;
    }
    ```
