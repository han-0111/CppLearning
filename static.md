# static关键字

两种含义：

- 类或结构体之外：类外的`static`关键字修饰的符号在链接阶段是局部的。只对当前所在的翻译单元可见（只在所在的文件中可见）其他的文件是无法访问被修饰的内容的。

`main.cpp`：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

int s_Variable = 10;  // 全局变量s_Variable 初始化为整型常量 10

int main()
{
	LOG(s_Variable);  // 打印输出变量 s_Variable
}
```

`static.cpp`：

```c++
static int s_Variable = 6;
```

在上面的这种文件结构下，编译无错误，输出结果是 10。

如果更改`static.cpp`中的内容为：

```c++
int s_Variable = 6;
```

此时**编译是可以通过的**，但是在运行时会引发**链接错误**:

![image-20230821223626212](https://github.com/han-0111/CppLearning/assets/75550897/742774ad-e84a-49a6-aeb2-228fd34fc16a)

链接器会找到一个或多个多重定义的符号，是因为链接器不仅会找到`main.cpp`中的变量`s_Variable`，而且由于`static.cpp`中的**变量不是静态的(static)**，也会被链接器找到，所以会引发该错误。

除上面的把`static.cpp`中的全局变量`s_Variable`用`static`修饰外，还有另外的解决方法：在`main.cpp`中，使用关键字`extern`修饰该全局变量:

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

extern int s_Variable;  // 表明该变量在其他的位置

int main()
{
	LOG(s_Variable);  // 打印输出变量 s_Variable
}
```

建议：尽量让全局变量或者函数使用`static`关键字修饰

- 类或结构体内部：表示所修饰的符号为类或结构体的所有实例所共享的。即使该类被多次实例化，但是被`static`修饰的符号只会有一个实例。比如在类的内部定义一个属性使用`static`修饰，那么当多次实例化该类时，这个属性只有一个：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

class Entity
{
public:
	int x, y;  // 两个普通的公有属性

	void Print()
	{
		/*输出两个属性*/
		std::cout << x << ' ' << y << std::endl;
	}
};

int main()
{
	Entity e;  // 实例化一次Entity类
	e.x = 0;
	e.y = 1;

	Entity e1 = {2, 3};  // 第二次实例化Entity类 初始化器可以直接给对象的属性赋值

	e.Print();  // 0 1
	e1.Print();  // 2 3
}
```

一切正常，但是当在类的内部使用`static`关键字来修饰两个属性时：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

class Entity
{
public:
	static int x, y;  // 两个静态的公有属性

	void Print()
	{
		/*输出两个属性*/
		std::cout << x << ' ' << y << std::endl;
	}
};

int main()
{
	Entity e;  // 实例化一次Entity类
	e.x = 0;
	e.y = 1;

	Entity e1 = {2, 3};  // 第二次实例化Entity类 初始化器可以直接给对象的属性赋值

	e.Print();  
	e1.Print();  
}
```

此时编译代码就会报错：

![image-20230821231050617](https://github.com/han-0111/CppLearning/assets/75550897/ef65483d-dc4f-41e2-9496-ed20253b7c01)

如果更改`e1`的初始化方式：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

class Entity
{
public:
	static int x, y;  // 两个静态的公有属性

	void Print()
	{
		/*输出两个属性*/
		std::cout << x << ' ' << y << std::endl;
	}
};

int main()
{
	Entity e;  // 实例化一次Entity类
	e.x = 0;
	e.y = 1;

	Entity e1;
	e1.x = 2;
	e1.y = 3;

	e.Print();  
	e1.Print();  
}
```

此时报错内容为：

![image-20230821231422170](https://github.com/han-0111/CppLearning/assets/75550897/a938149b-ae42-4948-a257-57dad8535f84)


无法解析的外部符号，那么我们就把这两个定义一下：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

class Entity
{
public:
	static int x, y;  // 两个静态的公有属性

	void Print()
	{
		/*输出两个属性*/
		std::cout << x << ' ' << y << std::endl;
	}
};

int Entity::x;  // 作用域::变量名
int Entity::y;  // 作用域::变量名

int main()
{
	Entity e;  // 实例化一次Entity类
	e.x = 0;
	e.y = 1;

	Entity e1;
	e1.x = 2;
	e1.y = 3;

	e.Print();
	e1.Print();
}
```

编译终于通过了，但是会发现一个很奇怪的现象，两个对象的属性值竟然是一样的，都是：`2 3`。可是明明对`e`的两个属性赋值为`0和1`啊。

这就是开始说到的：类或者结构体内部的`static`修饰的符号无论实例化几次，这些符号仅存在一个！！！会被最新的值覆盖掉！

拿上面的例子来说：`static`修饰了两个符号`x`和`y`，那么所有类的实例中的这两个属性指向的是同一个共享空间！

所以，**引用实例的静态内容是没有意义的，无论怎样那两个东西就是那两个东西**，所以一般可以使用下面的做法，通过`Entity::x`的方式来引用：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

class Entity
{
public:
	static int x, y;  // 两个静态的公有属性

	void Print()
	{
		/*输出两个属性*/
		std::cout << x << ' ' << y << std::endl;
	}
};

int Entity::x;
int Entity::y;

int main()
{
	Entity e;  // 实例化一次Entity类
	e.x = 0;
	e.y = 1;

	Entity::x = 5;
	Entity::y = 6;

	e.Print();  // 5 6
}
```

**注意事项：**

- 基于所有实例的静态内容都共享内容这一特性，可以把**需要所有实例都要使用**的相同内容设置为静态。

- 使用静态内容可以不实例化：

  ```c++
  #include<iostream>
  
  #define LOG(x) std::cout<<x<<std::endl
  
  class Entity
  {
  public:
  	static int x, y;  // 两个静态的公有属性
  
  	static void Print()  // 设置为静态方法
  	{
  		/*输出两个属性*/
  		std::cout << x << ' ' << y << std::endl;
  	}
  };
  
  int Entity::x;
  int Entity::y;
  
  int main()
  {
  	Entity::x = 5;
  	Entity::y = 6;
  	Entity::Print();
  }
  ```

- 静态方法不能访问非静态变量。静态的内容没有引用指针，**本质上类内的每个非静态方法都会获得当前的类实例作为参数**：

  ```c++
  void Print(Entity e)  // 传入类的实例
  {
  	/*输出两个属性*/
  	std::cout << e.x << ' ' << e.y << std::endl;
  }
  ```

  但是静态方法是没有这个隐藏的参数的，在实际运行中就类似下面这样：

![image-20230821235249366](https://github.com/han-0111/CppLearning/assets/75550897/5684dc28-19fa-4dbc-9357-a338356bd5dc)

  静态的方法由于没有隐藏参数并不知道实例对象是谁的实例！！



## 局部作用域中的`static`

- 变量的生命周期：变量实际的存在时间，也就是变量在被删除前在内存中停留多久。
- 作用域：可以访问该变量的范围，比如函数内部定义一个变量，通常是不能在其他函数里访问到这个变量，这样的变量称为局部(local)变量。
- 静态局部(local static)变量：声明一个变量，生命周期是整个程序的生存期。但作用域被限制在其所在作用域中。

示例代码：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

int main()
{
	for (int j = 0; j <= 4; j++)
	{
		static int v = 0;  // 静态局部变量 v 初始化为 0
		v++;  // v自加1
		LOG(v);  // 打印输出 v 的值
	}
}
```

上面的代码循环执行了5次，每次循环的内容为：

1. 定义一个静态局部变量v，并初始化为整型数字0；
2. v自加1；
3. 打印输出v的值, 结果为`1 2 3 4 5 `

如果去掉关键字`static`：

```c++
#include<iostream>

#define LOG(x) std::cout<<x<<std::endl

int main()
{
	for (int j = 0; j <= 4; j++)
	{
		int v = 0;  // 局部变量 v 初始化为 0
		v++;  // v自加1
		LOG(v);  // 打印输出 v 的值
	}
}
```

由于每次循环都重新定义了`v`，并初始化为0，所以每次输出的结果都是 1，也就是`1 1 1 1 1 `。

**这就是说，当使用`static`关键字修饰时，被修饰的内容的生命周期为程序的整个运行周期，在程序运行过程中，始终存在在内存当中，其中的值是一直存在的，而没有`static`关键字的话，就会在每次循环开始时，都会创建一个新的变量，在内存中设定一个新的地址用来存储对应的值。**
