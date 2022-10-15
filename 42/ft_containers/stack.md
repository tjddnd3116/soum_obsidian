# stack
>``` c
>template <class T, class Container = deque< T > >
>class stack;
>```

## stack이란?
 > stack은 나중에 들어간 것이 먼저 나오는 LIFO의 형태를 띠는 자료구조이다.

스택은 특정 컨테이너 클래스의 개체를 기본 컨테이너로 사용하는 클래스인 컨테이너 어댑터로 구현되어 있다.
기본 컨테이너는 다음 작업을 지원해야 한다.
- empty
- size
- back
- push_back
- pop_back

ft_containers 에서는 기본 컨테이너를 vector로 한다.

## 템플릿 인자
	T
요소의 타입이다.
stack::value_type인 멤버타입으로 앨리어싱된다.

	Container
stack의 요소가 저장되는 내부 기본컨테이너 개체의 타입이다.
기본컨테이너의 value_type은 T가 된다.
stack::container_type인 멤버타입으로 앨리어싱된다.

## Member types
|member type|definition|notes|
|-|-|-|
|*value_type*|The first template parameter(T)|Type of the elements|
|*container_type*|The second template parameter(Container)|Type of the underlying container|
|size_type|an unsigned integral type|usually the same as size_t|

## Member functions
|functions|notes|
|-|-|
|*(constructor)*|Construct stack|
|*empty*|Test whether container is empty|
|*size*|Return size|
|*top*|Access next element|
|*push*|Insert element|
|*pop*|Remove top element|


## Member functions
|functions|notes|
|-|-|
|*relational operators*|Relational operators for stack|

#ft_containers [[iterator]] [[vector]] 