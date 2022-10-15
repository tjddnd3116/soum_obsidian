# vector
> ``` c
template < class T, class Alloc = allocator< T > >
class vector;
> ```

## vector란?
> 벡터는 크기가 변할 수 있는 배열을 나타내는 시퀀스 컨테이너

배열과 마찬가지로 벡터는 요소에 대해 연속적인 저장 위치를 사용한다.
배열과 달리 크기는 동적으로 변경될 수 있다.
내부적으로 벡터는 동적으로 할당된 배열을 사용하여 요소를 저장한다.
이 배열은 새 요소가 삽입될 때 크기를 늘리기 위해 재 할당 해야 할 수 있다.
이는 새 배열을 할당하고 모든 요소를 새 배열로 복사하는 것을 의미한다.
이것은 시간 면에서 상대적으로 비용이 많이 드는 작업이므로 요소가 컨테이너에 추가 될때마다 벡터가 재할당 되지 않는다.

벡터는 새 요소를 수용하기위해 추가 용량을 할당할 수 있다.
따라서 벡터의 크기(size)보다 더 큰 실제 용량(capacity)을 가질 수 있다.

## 템플릿 매개변수
	T
요소의 타입이다. vector::value_type 인 멤버 타입으로 엘리어싱 된다.

	Alloc
벡터 내부의 할당하기위해 사용되는 할당자 개체의 타입이다.
vector::allocator_type 인 멤버 타입으로 앨리어싱된다.

## member type
| member type | definition | notes |
|---------------|----------|--------|
|value_type|The first template parameter(T)||
|allocator_type|The second template parameter(Alloc)|defaults to : allocator<value_type>|
|reference|allocator_type::reference|for the default allocator: value_type &|
|const_reference|allocator_type::const_reference|for the default allocator: const value_type &|
|pointer|allocator_type::pointer|for the default allocator: value_type*|
|[[iterator]]|a random access iterator to value_type|convertible to const_iterator|
|const_iterator|a random access iterator to const value_type||
|reverse_iterator|reverse_iterator\<iterator\>||
|const_reverse_iterator|reverse_iterator\<const_iterator\>||
|difference_type|iterator_traits\<iterator\>::difference_type|usually the same as ptrdiff_t|
|size_type|an unsigned integral type that can represent any non-negative value of difference_type|usually the same as size_t|
## member functions
|functions|notes|
|-|-|
|*(constructor)*|Construct vector|
|*(destructor)*|Vector destructor|
|*operator=*|Assign content|

Iterators:

|functions|notes|
|-|-|
|*begin*|Retrun iterator to beginning|
|*end*|Retrun iterator to end|
|*rbegin*|Retrun iterator to reverse beginning|
|*rend*|Retrun iterator to reverse end|

Capacity:

|functions|notes|
|-|-|
|*size*|Retrun size|
|*max_size*|Retrun maximum size|
|*resize*|Change size|
|*capacity*|Return size of allocated storage capacity|
|*empty*|Test whether vector is empty|
|*reserve*|Request a change in capacity|

Element access:

|functions|notes|
|-|-|
|*operator[]*|Access element|
|*at*|Access element|
|*front*|Access first element|
|*back*|Access last element|

Modifiers:

|functions|notes|
|-|-|
|*assign*|Assign vector content|
|*push_back*|Add element at the end|
|*pop_back*|Delete last element|
|*insert*|Insert elements|
|*erase*|Erase elements|
|*swap*|Swap content|
|*clear*|Clear content|

Allocator:

|functions|notes|
|-|-|
|*get_allocator*|Get allocator|

## Non-member function overloads
|functions|notes|
|-|-|
|*relational_operators*|Relational operators for vector|
|*swap*|Exchange contents of vectors|

#ft_containers [[iterator]]