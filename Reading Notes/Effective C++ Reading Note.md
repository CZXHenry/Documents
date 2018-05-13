# Effective C++ 读书笔记
## 条款25：考虑写出一个不抛出异常的swap函数
### 1. 目的
- swap函数非常有用，因此需要适当的实现。
- swap函数的典型实现是比较简单的，即使很简单对于一些 `pimpl手法` 实现的类型而言的，这种实现方法的执行效率还是很低的。当我们在程序中使用到这种类时，就需要考虑重新实现更适合的效率更高的swap函数，而不应该只依赖标准程序库提供的swap函数
> **pimpl手法是什么？**
> pimpl即是pointer to implementation的缩写，用这种手法设计的类可以参见 `Widget` 类

### 2. 主要问题
下面通过一些程序来解释为什么需要考虑写出一个不抛出异常的swap函数
```cpp
//一种典型的std::swap实现
template<typename T>
void swap(T& a,T& b)
{
	T temp(a);
	a=b;
	b=temp;
}
//使用pimpl手法实现一个Widget类
class WidgetImpl
{
public:
	WidgetImpl(int ma=0,int mb=0,int mc=0,vector<double> mv=vector<double>(0,0))
	{
		a=ma;
		b=mb;
		c=mc;
		v=mv;
	}
	int getA()
	{
		return a;
	}
	int getB()
	{
		return b;
	}
	int getC()
	{
		return c;
	}
	vector<double> getV()
	{
		return v;
	}

	void setA(int ma)
	{
		a=ma;
	}

	void setB(int mb)
	{
		b=mb;
	}

	void setC(int mc)
	{
		c=mc;
	}

	void setV(vector<double> mv)
	{
		v=mv;
	}
private:
	int a,b,c;
	vector<double> v;
};

class Widget
{
public:
	Widget()
	{
		while(pImpl==NULL)
		{
			pImpl=new WidgetImpl;
		}
	}
	Widget(const Widget& rhs)
	{
		if(pImpl==NULL)
		{
			pImpl=new WidgetImpl;
		}
		pImpl->setA(rhs.pImpl->getA());
		pImpl->setB(rhs.pImpl->getB());
		pImpl->setC(rhs.pImpl->getC());
		pImpl->setV(rhs.pImpl->getV());
	}

	Widget& operator=(const Widget& rhs)
	{
		if(&rhs!=this)
		{
			if(this->pImpl==NULL)
			{
				pImpl=new WidgetImpl;
			}
			pImpl->setA(rhs.pImpl->getA());
			pImpl->setB(rhs.pImpl->getB());
			pImpl->setC(rhs.pImpl->getC());
			pImpl->setV(rhs.pImpl->getV());

		}
		return *this;
	}
```
如果利用典型的 `swap` 函数来交换两个 `Widget` 对象，则需要：
- 调用复制构造函数来构造 `temp`,复制 `Widget` 类数据部分，然后把 `a.pImpl` 指向的内存地址中储存的数据全部复制一份到 `temp.pImpl` 所指向的内存地址。
- 调用重载的=操作符，复制 `Widget` 类数据部分，这里需要把 `b.pImpl` 指向的内存地址中储存的数据全部复制一份到 `a.pImpl` 所指向的内存地址
- 调用重载的=操作符，复制 `Widget` 类数据部分，这里需要把 `temp.pImpl` 指向的内存地址中储存的数据全部复制一份到 `b.pImpl` 所指向的内存地址。
由此可见，当利用典型的 `swap` 函数来交换两个 `Widget` 时，将会执行大量的复制工作（`Widget`复制三次，`WidgetImpl`对象也复制了三次）。如果`WigetImpl`对象中的容器储存了大量的数据，则复制三次的时间是非常长的。而实际上这三次复制并不是不需的，只要让 `a.pImpl`和`b.pimpl`分别指向对方原来地址即可，这样就已经实现了交换的目的，而不需要重新将指向的内存写一遍。
> 交换`Widget`指针值的方法和典型的`swap`方法差别在于，交换了指针值，相同内存位置的数据并没有发生变化；而典型的`swap` 方法则使指针值不变，但是指向的内存位置的数据变了。