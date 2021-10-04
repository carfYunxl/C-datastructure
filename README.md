##线性表的顺序存储表示

##linear_Table的定义与实现

##linear_Table.h
```
#ifndef _LINEAR_TABLE_H
#define _LINEAR_TABLE_H
#include <iostream>

using std::cout;
using std::cin;
using std::endl;

//线性表数组表示法，int类型
class linear_Table
{
	static const int INIT_SIZE = 100;
	static const int LIST_INREMENT = 100;
public:
	//构造函数
	linear_Table(int sz = 0) :pt(new int[INIT_SIZE]), size(sz), capacity(INIT_SIZE)
	{
		std::cout << "constructor!@@" << std::endl;
	}
	//析构函数
	~linear_Table()
	{
		delete[] pt;
		std::cout << "ddconstructor!@@" << std::endl;
	}
	//拷贝构造函数
	linear_Table(const linear_Table& rhs) :pt(new int(*rhs.pt)), size(rhs.size), capacity(rhs.capacity)
	{
		std::cout << "copy constructor!@@" << std::endl;
	}
	//拷贝赋值运算符
	const linear_Table& operator=(const linear_Table& rhs)
	{
		std::cout << "copy operator =" << std::endl;
		int* tmp = new int[rhs.size];
		pt = tmp;

		size = rhs.size;
		capacity = rhs.capacity;
		return *this;
	}
	//移动构造函数
	linear_Table(const linear_Table&& rhs) noexcept :pt(new int(*rhs.pt)), size(rhs.size), capacity(rhs.capacity)
	{
		std::cout << "move constructor!@@" << std::endl;
	}
	//移动赋值运算符
	const linear_Table& operator=(const linear_Table&& rhs) noexcept
	{
		std::cout << "move operator =" << std::endl;
		int* tmp = new int[rhs.size];
		pt = tmp;

		size = rhs.size;
		capacity = rhs.capacity;
		return *this;
	}


	//interface
	//打印表信息
	void print();
	//清空线性表
	void clear_linear_table();
	//在尾部插入一个元素
	void push(int a);
	//在位置i插入一个元素
	void insert(int i, int x);
	//表判空
	bool empty()
	{
		return size > 0 ? false : true;
	}
	//表长
	int length()
	{
		return size;
	}
	//取指定位置的元素
	int Get(int index)
	{
		return pt[index - 1];
	}
	//取指定元素的位置
	int locate(int element);
	//返回指定元素的前一个元素
	int priorElem(int element);
	//返回指定元素的后一个元素
	int nextElem(int element);

	//删除指定元素
	void deleteElem(int element);

private:
	int* pt;//数组基址
	int size;//当前表长
	int capacity;//分配的总容量
};


//合并两个线性表,将lb中不存在于la的元素并入la
void linear_table_add(linear_Table& la, linear_Table& lb);

//将两个有序表并入一个表中，并保持新表有序
void linear_table_merge(linear_Table& la, linear_Table& lb,linear_Table & lc);
#endif//_LINEAR_TABLE_H
```

##linear_Table.cpp
```
#include "linear_Table.h"

//在尾部插入一个元素
void linear_Table::push(int a)
{
	if (size < capacity)
	{
		pt[size] = a;
		size++;
		return;
	}
	int* newBase = new int[capacity + LIST_INREMENT];
	for (int i = 0; i < size; ++i)
	{
		newBase[i] = pt[i];
	}
	std::swap(pt, newBase);
	delete[] newBase;
	capacity += LIST_INREMENT;
	pt[size] = a;
	size++;
}

//清空线性表
void linear_Table::clear_linear_table()
{
	for (int i = 0; i < size + 1; ++i)
	{
		pt[i] = 0;
	}
	size = 0;
	capacity = INIT_SIZE;
}

//打印表信息
void linear_Table::print()
{
	for (int i = 0; i < size; ++i)
	{
		std::cout << pt[i] << std::endl;
	}
	std::cout << "pt = " << pt << " size =" << size << " capacity = " << capacity << std::endl;
}
void linear_Table::insert(int i, int x)
{
	if (i < 0 || i > size)
	{
		std::cout << "Failure:index i out of range" << std::endl;
		return;
	}
	if (size == i)
	{
		push(x);
		return;
	}
	if (size == capacity)
	{
		int* tmp = new int[capacity + LIST_INREMENT];
		for (int i = 0;i < size;++i)
		{
			tmp[i] = pt[i];
		}
		std::swap(pt,tmp);
		delete[] tmp;

		for (int k = size - 1; k >= i - 1; k--)
		{
			pt[k + 1] = pt[k];
		}
		pt[i - 1] = x;
		size++;
		capacity += LIST_INREMENT;
		return;
	}
	for (int k = size - 1;k >= i - 1;k--)
	{
		pt[k+1] = pt[k];
	}
	pt[i-1] = x;
	size++;
}
int linear_Table::locate( int element )
{
	int tmp = 0;
	for (int i = 0; i < size; ++i)
	{
		if ( pt[i] == element )
		{
			return i+1;
			tmp++;
		}
	}
	if (tmp == 0)
		std::cout << "element " << element << " not in the table!" << std::endl;
	return 0;
}

//返回指定元素的前一个元素
int linear_Table::priorElem(int element)
{
	for (int i = 0; i < size; ++i)
	{
		if (pt[i] == element)
		{
			return pt[i - 1];
		}
		return -1;
	}
	return -1;
}
//返回指定元素的后一个元素
int linear_Table::nextElem(int element)
{
	for (int i = 0; i < size; ++i)
	{
		if (pt[i] == element)
		{
			return pt[i + 1];
		}
		return -1;
	}
	return -1;
}

void linear_Table::deleteElem(int element)
{
	for (int i = 0; i < size; ++i)
	{
		if (pt[i] == element)
		{
			for (int k = i; k < size; k++)
			{
				pt[k] = pt[k+1];
			}
			size--;
		}
	}
}

void linear_table_add(linear_Table& la,linear_Table& lb)
{
	for (int i = 1;i <= lb.length();++i)
	{
		if (!la.locate(lb.Get(i)))
		{
			la.push(lb.Get(i));
		}
	}
}


void linear_table_merge(linear_Table& la, linear_Table& lb, linear_Table& lc)
{
	int i = 1;
	int j = 1;
	//3 5 7 9 12
	//4 6 8 9 16
	//3 4 5 6 7 8 9 9 12 16
	while (i <= la.length() && j <= lb.length())
	{
		if (la.Get(i) < lb.Get(j))
		{
			lc.push(la.Get(i));
			i++;
		}
		else
		{
			lc.push(lb.Get(j));
			j++;
		}
	}
	if (6 == i)
	{
		for (;j <= lb.length();j++)
		{
			lc.push(lb.Get(j));
		}
	}
	if (6 == j)
	{
		for (; i <= la.length(); i++)
		{
			lc.push(la.Get(i));
		}
	}
}
```

##linear_List.h
```
#ifndef _LINEAR_LIST_H
#define _LINEAR_LIST_H
#include <iostream>
#include <vector>

using std::cout;
using std::endl;

class node
{
public:
	node(int dt) :data(dt), next(nullptr)
	{
		std::cout << " node @@c! " << std::endl;
	}
	~node()
	{
		std::cout << " node dd ##c! " << std::endl;
	}
	int data;
	node* next;
};

class linear_List
{
public:
	//构造函数
	linear_List() { std::cout << " constructor! " << std::endl; }

	linear_List(const int size, const int arr[]);
	//析构函数
	~linear_List()
	{ 
		for (int i = 0;i < v_node.size();++i)
		{
			delete v_node[i];
		}
		std::cout << " ddconstructor! " << std::endl; 
	}
	//拷贝构造函数
	linear_List(const linear_List& rhs)
	{
		init(rhs);
		std::cout << " copy constructor! " << std::endl;
	}

	//拷贝赋值运算符
	const linear_List& operator=(const linear_List& rhs)
	{
		init(rhs);
		std::cout << " copy =! " << std::endl;
		return *this;
	}


	//interface
	//打印链表信息
	void print();
	//在尾部插入一个元素
	void pushback(int x);
	//链表长度
	int size() const;
	//获取某个索引处的元素
	int Get(int index) const;
	//j将线性表重置为空表
	void Clear();
private:
	node* head = nullptr;			//头指针
	std::vector<node*> v_node;		//管理所有的结点信息
	//std::shared_ptr<node*> head = nullptr;

	void init(const linear_List& rhs);
};
#endif //_LINEAR_LIST_H
```

##linear_List.cpp
```
#include "linear_List.h"
//构造函数
linear_List::linear_List(const int size, const int arr[])
{
	for (int i = 0; i < size; ++i)
	{
		node* next_node = new node(arr[i]);
		v_node.push_back(next_node);
		if (i == 0)
		{
			head = next_node;
		}
		else
		{
			node* p = head;
			//std::shared_ptr<node*> p = head;
			while (p->next)
			{
				p = p->next;
			}
			p->next = next_node;
		}
	}
}
//打印链表信息
void linear_List::print()
{
	node* p = head;
	//std::shared_ptr<node*> p = head;
	while (p)
	{
		std::cout << p->data << std::endl;
		p = p->next;
	}

	for (int i = 0; i < v_node.size(); ++i)
	{
		std::cout << v_node[i]->data << std::endl;
	}
	//delete p;
}
//在尾部插入数据
void linear_List::pushback(int x)
{
	node* next_node = new node(x);
	///std::shared_ptr<node*> next_node = std::make_shared<node*>(new node(x));
	v_node.push_back(next_node);
	if (head == nullptr)
	{
		head = next_node;
		return;
	}
	node* p = head;		//没有分配内存，不需要回收内存，易错
	//std::shared_ptr<node*> p = head;
	while (p->next)
	{
		p = p->next;
	}
	p->next = next_node;
}
int linear_List::size() const
{
	int tmp = 0;
	node* p = head;		//没有分配内存，不需要回收内存，易错
	//std::shared_ptr<node*> p = head;
	while (p)
	{
		p = p->next;
		tmp++;
	}
	return tmp;
}

int linear_List::Get(int index) const
{
	if (index < 1 || index > size())
	{
		std::cout << "Failure : index out of range!" << std::endl;
		return -1;
	}
	node* p = head;
	//std::shared_ptr<node*> p = head;
	for (int i = 0; i < index - 1; ++i)
	{
		p = p->next;
	}
	return p->data;
}

void linear_List::init(const linear_List& rhs)
{
	for (int i = 0; i < rhs.size(); ++i)
	{
		//std::shared_ptr<node*> next_node = std::make_shared<node*>(new node(rhs.Get(i + 1)));
		node* next_node = new node(rhs.Get(i + 1));
		v_node.push_back(next_node);
		if (i == 0)
		{
			head = next_node;
		}
		else
		{
			node* p = head;
			//std::shared_ptr<node*> p = head;
			while (p->next)
			{
				p = p->next;
			}
			p->next = next_node;
		}
	}
}
void linear_List::Clear()
{
	head = nullptr;
	for (int i = 0;i < v_node.size();++i)
	{
		delete v_node[i];
	}
}
```
