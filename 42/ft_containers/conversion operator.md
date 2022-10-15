# conversion operator
	객체에서 일반 타입 값을 받을 수 있다.

```c++
#include <iostream>
using namespace std;

class Distance
{
private :
	int killometer, meter;
public :
	Distance() : kilometer(0), meter(0){}
	explicit Distance(int newDist)
	{
		killometer = newDist / 1000;
		meter = newDist % 1000;
	}
	operator int()
	{
		return (killometer * 1000 + meter);
	}
	void printDistance()
	{
		cout << "Distance is " << killometer
		<< "km " << meter << "m\n";
	}
};

int main(void)
{
	Distance d(2030);
	int nDist = d;
	cout << "Value is " << nDist << endl;

	return (0);
}
```
operator int()로 된 부분이 변환 연산자이다.
객체를 리턴해서 nDist의 변수에 값을 저장할 수 있다.

#ft_containers [[iterator]]