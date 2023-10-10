# 类(class)

面向对象编程是一种编程思想，不注重解决问题的详细过程，而是设计具有相应属性和方法的对象，通过这些具有不同功能的对象来完成任务。

类是一种将数据和函数组织在一起的方式

- C++中的类的示例：

  ```c++
  #include<iostream>
  
  #define LOG(x) std::cout << x << std::endl
  
  class Player  // class 关键字 表示这是一个类, Player 类的名称
  {
  public:  // public: 表示冒号后面的内容为公有内容，可以从类的外部进行访问
  	int x = 0;  // 定义类的两个属性, 在类中的变量通常称为 属性
  	int y = 0;  // 表示玩家的位置坐标
  
  	void Move(int stride)  // 定义一个方法, 在类中的函数通常称为 方法
  	{
  		/*该方法实现移动玩家位置*/
  		x = x + stride;  // x不需要额外定义，在类的内部就是该类的属性x
  		y = y - stride;
  	}
  
  };  // 类的定义要有 ; 作为结束
  
  
  int main()
  { 
  	Player one;  // 创建一个Player类的对象, 通常称为 实例化一个对象
  	one.x = 120;  // 通过 对象名.属性 取到对应的属性
  	one.y = 120;
  	one.Move(5);  // 通过 对象名.方法 执行相应的方法
  	LOG(one.x);
  	LOG(one.y);
  }
  ```

`class`: 关键字，表明这是一个类。后面接类名，然后是一对`{}`，最后要有一个`;`。

`public`：关键字，接一个`:`，后面接属性或方法。表明后面的内容是公有的，可以让类外面的成员访问。（c++的类中的内容默认为私有(private)，外部是不可以直接访问的）

`属性和方法`: 类内的数据通常称为属性，函数通常称为方法。

## class(类) VS struct(结构体)

- 一个class的成员默认情况是private(私有)的，而struct正好相反；

- c++中保留struct是考虑到与c的兼容性，c中是没有class的。简单的方式完成两种代码的转换只需要一个宏定义：

  ```c++
  在c++代码种使用
      #define class struct
  
  在c代码中使用
      #define struct class
  ```

![image-20230819175251023](https://github.com/han-0111/CppLearning/assets/75550897/c3cbd4c4-e77e-48c2-a453-0e7a091380a9)

  这样就把`c++`中的`class`变成了`struct`, 反之亦然。

- 何时使用`class`以及什么时候使用`struct`看自己的编程风格了（毕竟两者的区别就是上面两个而已<'' -- ''>），建议如果需要使用的某个东西仅包含属性(数据)，那么就用`struct`，如果这个东西除了属性还有其他功能的方法那就用`class`吧。

## 类的写法

写一个日志类，用来打印不同的信息：

```c++
#include<iostream>

class Log
{
	/*Log类, 根据不同的级别设置来打印不同的信息*/
public:
	/*设置三种日志级别*/
	const int LogLevelError = 0;
	const int LogLevelWarning = 1;
	const int LogLevelInfo = 2;
private:
	int m_LogLevel = LogLevelInfo;  // 私有属性, 默认日志级别为Info

public:
	void SetLevel(int level)
	{
		/*设置日志打印信息的级别*/
		m_LogLevel = level;
	}

	void Error(const char* message)
	{
		/*错误信息*/
		if (m_LogLevel >= LogLevelError)
			std::cout << "[ERROR]:" << message << std::endl;
	}

	void Warn(const char* message)
	{
		/*警告信息*/
		if (m_LogLevel >= LogLevelWarning)
			std::cout << "[WARNING]:" << message << std::endl;
	}

	void Info(const char* message)
	{
		/*所有信息*/
		if (m_LogLevel >= LogLevelInfo)
			std::cout <<"[INFO]:" << message << std::endl;
	}
};

int main()
{
	Log log;  // 实例化一个Log类，用来打印日志信息
	log.SetLevel(log.LogLevelInfo);  // 设置日志信息的级别
	log.Warn("There is a warning!");  // 打印日志中的警告信息
	log.Error("There is an erron!!");  // 打印日志中的警告信息
	log.Info("There is information.");  // 打印日志中的信息
}
```



上述代码仅作示例，有很多问题而且非常不规范。接下来逐步完善上面的代码。
