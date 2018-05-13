# Effective C++ ����ʼ�
## ����25������д��һ�����׳��쳣��swap����
### 1. Ŀ��
- swap�����ǳ����ã������Ҫ�ʵ���ʵ�֡�
- swap�����ĵ���ʵ���ǱȽϼ򵥵ģ���ʹ�ܼ򵥶���һЩ `pimpl�ַ�` ʵ�ֵ����Ͷ��Եģ�����ʵ�ַ�����ִ��Ч�ʻ��Ǻܵ͵ġ��������ڳ�����ʹ�õ�������ʱ������Ҫ��������ʵ�ָ��ʺϵ�Ч�ʸ��ߵ�swap����������Ӧ��ֻ������׼������ṩ��swap����
> **pimpl�ַ���ʲô��**
> pimpl����pointer to implementation����д���������ַ���Ƶ�����Բμ� `Widget` ��

### 2. ��Ҫ����
����ͨ��һЩ����������Ϊʲô��Ҫ����д��һ�����׳��쳣��swap����
```cpp
//һ�ֵ��͵�std::swapʵ��
template<typename T>
void swap(T& a,T& b)
{
	T temp(a);
	a=b;
	b=temp;
}
//ʹ��pimpl�ַ�ʵ��һ��Widget��
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
������õ��͵� `swap` �������������� `Widget` ��������Ҫ��
- ���ø��ƹ��캯�������� `temp`,���� `Widget` �����ݲ��֣�Ȼ��� `a.pImpl` ָ����ڴ��ַ�д��������ȫ������һ�ݵ� `temp.pImpl` ��ָ����ڴ��ַ��
- �������ص�=������������ `Widget` �����ݲ��֣�������Ҫ�� `b.pImpl` ָ����ڴ��ַ�д��������ȫ������һ�ݵ� `a.pImpl` ��ָ����ڴ��ַ
- �������ص�=������������ `Widget` �����ݲ��֣�������Ҫ�� `temp.pImpl` ָ����ڴ��ַ�д��������ȫ������һ�ݵ� `b.pImpl` ��ָ����ڴ��ַ��
�ɴ˿ɼ��������õ��͵� `swap` �������������� `Widget` ʱ������ִ�д����ĸ��ƹ�����`Widget`�������Σ�`WidgetImpl`����Ҳ���������Σ������`WigetImpl`�����е����������˴��������ݣ��������ε�ʱ���Ƿǳ����ġ���ʵ���������θ��Ʋ����ǲ���ģ�ֻҪ�� `a.pImpl`��`b.pimpl`�ֱ�ָ��Է�ԭ����ַ���ɣ��������Ѿ�ʵ���˽�����Ŀ�ģ�������Ҫ���½�ָ����ڴ�дһ�顣
> ����`Widget`ָ��ֵ�ķ����͵��͵�`swap`����������ڣ�������ָ��ֵ����ͬ�ڴ�λ�õ����ݲ�û�з����仯�������͵�`swap` ������ʹָ��ֵ���䣬����ָ����ڴ�λ�õ����ݱ��ˡ�