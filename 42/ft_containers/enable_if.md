# 배경
c++에서 템플릿 함수 오버로딩의 합리적인 작동은 _**SFINAE**_ 원칙에 의존한다.  
함수 템플릿의 인스턴스화 중에 잘못된 인수 또는 잘못된 리턴값이 형성되면 인스턴스는 컴파일 오류를 일으키는 대신 오버로딩에서 제거된다.
``` c
int negate(int i) { return -i; }

template <class F>
typename F::result_type negate(const F& f) { return -f(); }
```
컴파일러는 두 함수를 모두 고려해야한다. int를 사용하여 후자의 정의를 인스턴스화하면 다음과 같이 된다.
``` c
int::result_type negate(const int& );
```
여기서는 반환유형이 유효하지가 않다. 그러나 _**SFINAE**_ 원칙으로 인해 위의 예는 잘못된 것이 아니다.
enable_if 템플릿은 _**SFINAE**_ 조건 생성을 제어하기 위한 도구이다.

# enable_if 템플릿
_**SFINAE**_ 를 잘 활용하는 툴들 중 가장 널리 쓰이는 것이  _**enable_if**_ 이다.
``` c
template <bool, typename T = void>
struct enable_if {};

tempate <typename T>
struct enable_if<true, T>
{
	typedef T type;
};
```
그리고 아래와 같은 일을 할 수 있다.
``` c
template <class T, typename std::enable_if<std::is_integral<T>::value, T::type* = nullptr>
void do_stuff(T& t) {
	std::cout << "do_stuff integral\n";
	// 정수 타입들을 받는 함수 (int, char, unsigned, etc.)
}

template <class T,
			typename std::enable_if<std::is_class<T>::value, T>::type* = nullptr>
void do_stuff(T& t){
	// 일반적인 클래스들을 받음
}
```
우리가 **do_stuff(int 변수)** 와 같이 함수를 호출한다면 컴파일러는 첫번째 오버로딩을 고르게된다. 
왜냐하면 **std::is_integral\<int\>** 가 참이기 때문이다.
이때 두번째 오버로딩은 후보군에서 제외되는데 **std::is_calss\<int\>** 가 false 이므로 내부에 type이 정의되지 않는 일반적인 형태의 struct enable_if 가 선택되어 치환 오류가 발생하기 때문이다.

# enable_if 사용
enable_if 함수 템플릿과 관련하여 다음과 같이 여러 방법으로 사용할 수 있다.
- 인스턴스화된 함수의 반환유형으로
- 인스턴스화된 한수의 추가 매개변수로
-  추가 템플릿 매개변수로
enable_if 추가 함수 매개변수를 통해 작동하는 형식을 사용하는 예로서 다음과 같이 작성할 수도 있다.
``` c
template <class T>
T foo (T t,
	  typename enable_if <is_arithmetic<T>::type* dummy = 0);
```




#SFINAE #ft_containers