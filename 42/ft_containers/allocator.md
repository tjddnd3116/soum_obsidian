# allocator
> allocator는 유연한 메모리 관리를 위한 클래스로 할당자라고도 한다.

## allocator 란?
일반적으로 메모리를 동적으로 할당하고 해제할때 new/delete연산자를 사용하는데 allocator 클래스는 주로 라이브러리를 작성할때, 특히 컨테이너를 구현할 때 많이 사용된다.
메모리를 좀 더 세밀하고 우연하고 효율적으로 사용해야 할 경우에 사용한다.

그래서 무엇이 좋을까?

	1. 메모리를 동적 할당할 경우 초기화되지 않은 공간으로 메모리를 동적할당 할 수 있다.
	2. 할당받은 메모리에 객체를 생성후 메모리 해제없이 생성한 객체들을 소멸 시킬수 있다. 즉, 메모리 재할당 없이 그 공간을 동적할당 했던 초기 상태로 만들 수 있다.

## 템플릿 파라미터
	T
요소의 타입이다. value_type 으로 앨리어싱 된다.

##  Member types
|member|definition in allocator|represents|
|-|-|-|
|*value_type*|T|Element type|
|*pointer*|T*|Pointer to Element|
|*reference*|T&|Reference to element|
|*const_pointer*|const T*|Pointer to constant element|
|*const_reference*|const T&|Reference to constant element|
|*size_type*|size_t|Quantities of elements|
|*difference_type*|ptrdiff_t|Difference between two pointers|
|*rebind\<Type\>*|ptrdiff_t|Difference between two pointers|

## Member functions
|function|notes|
|-|-|
|*(constructor)*|Construct allocator object|
|*(destructor)*|Allocator destructor|
|*address*|Return address|
|*allocate*|Allocate block of storage|
|*deallocate*|Release block of storage|
|*max_size*|Maximum size posiible to allocate|
|*construct*|Construct an object|
|*destroy*|Destroy an object|

**allocate** 함수는 초기화 되지 않은 메모리 공간을 할당하여 그 시작 주소를 반환하는 함수이다.

**deallocate** 함수는 메모리 공간을 해제하는 함수이다.

**construct** 함수는 초기화되지 않은 공간에 요소를 저장하는 함수이다.

**destroy** 함수는 객체를 소멸시킨다.
#ft_containers 