##### 1.如果定义了复制构造函数，就不会再定义默认构造函数！
```
class B{
public:
	B(const B& other){
		std::cout<<"B(const B&)"<<std::endl;	
	}
};

void test(){
	B b;//没有默认构造函数
}
```

##### 2.不论何时，只要没有定义复制构造函数，都会合成一个！
```
class B{
public:
	B(){
		std::cout<<"B()"<<std::endl;
	}
};

void test(){
	B b1;
	B b2(b1);//ok
}
```

##### 3.如果需要重写析构函数，一般也要重写复制，赋值函数！

##### 4.父类，子类，构造，析构的执行顺序
- 构造：父类-->子类
- 析构：子类-->父类

##### 5.虚析构函数的作用
```
class B{
public:
	B(){
		std::cout<<"B()"<<std::endl;
	}
  //virtual ~B(){
	~B(){
		std::cout<<"~B()"<<std::endl;
	}
};
class C:public B{
public:
	C(){
		std::cout<<"C()"<<std::endl;
	}
	~C(){
		std::cout<<"~C()"<<std::endl;
	}
};
void test(){
	B* b = new C;
	delete b;//不会调用子类的析构函数！
}
```

##### 6.纯虚析构函数，必须要有定义！
```
class B{
public:
	B(){
		std::cout<<"B()"<<std::endl;
	}
	virtual ~B() = 0;
};
B::~B(){//纯虚析构函数的定义！必须要有，会在子类析构的时候被调用！
	std::cout<<"~B()"<<std::endl;
}
class C:public B{
public:
	C(){
		std::cout<<"C()"<<std::endl;
	}
	~C(){
		std::cout<<"~C()"<<std::endl;
	}
};
void test(){
	B* b = new C;
	delete b;
}
```

##### 7.除了析构函数，所有的虚函数，纯虚的，不是纯虚的，都必须在子类中进行实现，不然编译不过！

##### 8.如果定义了虚函数但是没有实现这个虚函数，将无法定义相应的对象。
```
class A{
public:
	A(){
		std::cout<<"A()"<<std::endl;
	}
	virtual void display();
	~A(){
		std::cout<<"~A()"<<std::endl;
	}
};
int main(){
	A a;//error!
	std::cout<<sizeof(A)<<std::endl;
}
```

##### 9.malloc和free不会执行构造和析构函数
```
class A{
public:
	A(){
		std::cout<<"A()"<<std::endl;
	}
	~A(){
		std::cout<<"~A()"<<std::endl;
	}
};
int main(){
	A* pa = (A*)malloc(sizeof(A));
	free(pa);
}
```

##### 10.四种线程状态，新生态，死亡态，被阻塞态，运行态！
- 运行态和阻塞态之间的转换（sleep，等待IO完成，加锁）

##### 11.线程的同步，通信，互斥！
- 同步是指消息传递（消息的依赖关系更强）
- 通信也是消息传递
- 互斥是共享资源的互斥

##### 12.调试已经运行的程序（带符号表-g选项的）
1. gdb | attach pid
2. gdb a.out pid

##### 13.STL中常用的玩意有 vector（遍历删除的时候要用到返回值）map（红黑树）set(自动排序) queue（push，pop）

##### 14.strcpy包含尾零，strlen不包含尾零！
```
class String  
{  
public:  
    String(const char *str=NULL);//普通构造函数  
    String(const String &str);//拷贝构造函数  
    String & operator =(const String &str);//赋值函数  
    ~String();//析构函数  
protected:  
private:  
    char* m_data;//用于保存字符串  
};  
  
//普通构造函数  
String::String(const char *str)
{  
    if (str==NULL)
    {  
        m_data=new char[1]; //对空字符串自动申请存放结束标志'\0'的空间  
        if (m_data==NULL)
        {//内存是否申请成功  
          std::cout<<"申请内存失败！"<<std::endl;  
          exit(1);  
        }  
        m_data[0]='\0';  
    }  
    else
    {  
        int length=strlen(str);  
        m_data=new char[length+1];  
        if (m_data==NULL)
        {//内存是否申请成功  
            std::cout<<"申请内存失败！"<<std::endl;  
            exit(1);  
        }  
        strcpy(m_data,str);  
    }  
}  

//拷贝构造函数  
String::String(const String &other)
{ //输入参数为const型  
    int length=strlen(other.m_data);  
    m_data=new char[length+1];  
    if (m_data==NULL)
    {//内存是否申请成功  
        std::cout<<"申请内存失败！"<<std::endl;  
        exit(1);  
    }  
    strcpy(m_data,other.m_data);  
}  

//赋值函数  
String& String::operator =(const String &other)
{//输入参数为const型  
    if (this == &other) //检查自赋值  
    {  return *this; }

    delete [] m_data;//释放原来的内存资源  

    int length=strlen(other.m_data);     
    m_data= new char[length+1];  
    if (m_data==NULL)
    {//内存是否申请成功  
        std::cout<<"申请内存失败！"<<std::endl;  
        exit(1);  
    }  
    strcpy(m_data,other.m_data);  

    return *this;//返回本对象的引用  
}  

//析构函数  
String::~String()
{  
    delete [] m_data;  
}  
```


- RAII
- 就是我的AutoRelase模板
- 资源包括文件句柄，socket，数据库连接，锁，数据库连接！
##### 为什么要使用 shared_from_this()，而不是直接shared_ptr<>(this);
- 如果一个对象已经被shared_ptr接管了，想要在对象内部获取shared_ptr就不太可能了。因为shared_ptr<>(this)两次！这样会造成两次泄漏！
- shared_from_this的实现原理是，通过weak_ptr实现！然后从weak_ptr提升到shared_ptr！
