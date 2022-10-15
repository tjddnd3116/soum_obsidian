# map
> ```c
> template <class Key,
> class T,
>	class Compare = less< key >,
>	class Alloc = allocator<pair<const Key,T>>
>	class map;
> ```

## map이란?
>map은 각 노드가 key와 value 쌍으로 이루어진 트리이다.
>특히, 중복을 허용하지 않는다.

map은 first, second가 있는 [[pair]]객체로 저장된다.
first는 key로, second는 value로 저장된다.
map은 자료를 저장할때 내부에서 자동으로 정렬된다.
key 기준으로 정렬하며 오름차순으로 정렬한다.
map에 매핑된 값은 대괄호 연산자 (operator[])를 사용하여 해당 키로 직접 액세스할 수 있다.
map은 일반적으로 [[red black tree]]로 구현된다.

## 템플릿 매개변수
	Key
맵의 각 요소는 키 값으로 고유하게 식별된다.
map::key_type인 맴버 타입으로 앨리어싱된다.

	T
맵의 값의 타입이다. 맵의 각 요소는 T타입으로 데이터를 저장한다.
map::mapped_type인 맵버 타입으로 앨리어싱된다.

	Compare
comp(a, b)는 a와 b의 키 값을 비교하여 bool 값을 리턴한다.

	Alloc
map 내부의 요소를 할당하기위해 사용되는 할당자 개체의 타입이다.
map::allocator_type 인 멤버 타입으로 앨리어싱된다.


#ft_containers [[red black tree]] [[iterator]]