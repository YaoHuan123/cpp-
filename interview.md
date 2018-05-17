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
1.gdb | attach pid
2.gdb a.out pid
