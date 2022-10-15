# set
>```c
>template < class T,
>	class Compare = less< T >,
>	class Alloc = allocator< T > >
>class set;
>```

## set이란?
	set은 Key라 불리는 원소들의 집합으로 이루어진 컨테이너이다.
연관 컨테이너중 하나이다.
노드 기반 컨테이너 이며 [[red black tree]]로 구현되어 있다.
Key값은 중복이 허용 되지 않는다.
원소가 삽입이되면 원소는 자동으로 정렬이 된다.
default 정렬기준은 less(오름차순)이다.

## 템플릿 매개변수
	T
요소의 타입이다.
set 컨테이너의 각 요소도 이 값으로 교유하게 식별된다.
set::key_type, set::value_type인 맴버 타입으로 앨리어싱된다.

	Compare
comp(a, b)는 a와 b의 키 값을 비교하여 bool 값을 리턴한다.

	Alloc
set 내부의 요소를 할당하기위해 사용되는 할당자 개체의 타입이다.
set::allocator_type 인 멤버 타입으로 앨리어싱된다.

#ft_containers [[red black tree]] 