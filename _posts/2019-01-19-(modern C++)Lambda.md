---

layout: post
title: [Modern C++] lambda의 특징과 사용법

description: >



tags: [neweins]

---

# [Modern C++] lambda의 특징과 사용법

[출처](https://blog.koriel.kr/modern-cpp-lambdayi-teugjinggwa-sayongbeob/) 

lambda는 람다 표현식 또는 람다 함수, 익명 함수(anonymous function)로 불립니다. 그 성질은 함수 객체(functor)와 동일합니다. 그 이름처럼 몸통은 있지만 이름이 없는 함수입니다. 요즘 대부분의 프로그래밍 언어들은 이 lambda를 지원하고 있습니다.

lambda는 함수 객체 클래스를 암시적으로 정의하고 함수 객체를 생성합니다. lambda로 생성된 함수 객체는 타입을 가지고 있긴 하지만 decltype이나 sizeof를 사용할 순 없습니다.


## 1. 함수 객체 vs lambda
C++ 프로그래밍을 하다 보면 함수 포인터나 함수 객체가 필요한 시점이 있습니다. 함수 포인터는 Callback 함수를 상황에 따라 다르게 하거나 Strategy 패턴을 구현할 때 주로 씁니다. 하지만 함수 포인터는 상태를 가질 수 없기 때문에 상태를 가져야하는 함수가 필요할 땐 함수 객체를 씁니다. 둘 다 장단점이 있습니다. 함수 객체의 가장 큰 단점은 번거로움이죠. 함수 객체를 정의할 땐 클래스나 구조체를 정의해야 합니다. 코드도 길어지고 보기도 좋지 않습니다.

lambda는 함수 포인터와 함수 객체의 장점만 골라 가졌다고 볼 수 있습니다. 정의하기 위해서 클래스나 구조체를 정의할 필요가 없고가 상태도 가질 수 있습니다.

예시로 같은 기능의 함수를 함수 객체와 lambda로 모두 구현해보고 lambda가 함수 객체에 비해 얼마나 편리한 지 알아보겠습니다.

~~~c
#include <array>
#include <algorithm>

// 함수 객체로 구현
struct SumFunctor : public std::unary_function<int, void> {
    SumFunctor(int& number) : sum(number) {}
    
    void operator() (int& number) {
        sum += number;
    }
    
private:
    int& sum;
};

int main() {
    std::array<int, 5> numbers = { 1, 2, 3, 4, 5 };
    int sum = 0;
    
    std::for_each(numbers.begin(), numbers.end(), SumFunctor(sum));
    
    sum = 0;
    
    // lambda로 구현
    std::for_each(numbers.begin(), numbers.end(), [&sum](int& number) { 
        sum += number; 
    });
    
    return 0;
}
~~~

합계를 저장할 변수를 참조자로 전달받아 거기에 값을 누적시키는 함수를 구현했습니다. 함수 객체로 구현할 경우 번거롭게 구조체를 먼저 정의한 후 사용해야 합니다. 하지만 lambda는 그렇지 않죠. 단 몇줄만으로 끝났습니다. 굉장히 섹시합니다. 이제 왜 써야하는지는 알았으니 어떻게 쓰는지를 알아봅시다.


## 2. lambda의 사용법

### 2-1. lambda의 문법
lambda의 문법은 크게 캡처(capture), 인자(parameter), 반환형(return type), 몸통(body)로 이루어져 있습니다.

~~~c
[captures](parameters) -> return type { body }

/*
* captures: comma(,)로 구분된 캡처들이 들어갑니다.
* parameters: 함수의 인자들이 들어갑니다.
* return type: 함수의 반환형입니다.
* body: 함수의 몸통입니다.
*/
~~~

캡처를 제외한 나머지 것들은 대부분 아시리라 봅니다. 그냥 함수와 동일합니다. 캡처는 lambda에서 사용할 변수나 상수들을 미리 지정하여 찍어오는 것입니다. 참조하여 찍어올 수도 있고 복사하여 찍어올 수도 있는데 그건 천천히 예시를 보면서 알아보겠습니다.

~~~c
#include <iostream>

int main() {
    // no parameter, no argument.
    []() { std::cout << "Hello lambda!" << std::endl; }();
    
    return 0;
}
~~~

### 2-2. lambda의 parameter

~~~c
#include <iostream>

int main() {
    [](std::string name) {
        std::cout << "My name is " << name << std::endl;
    }("Jinsoo Heo");
    
    return 0;
}
~~~

### 2-3. lambda를 std::function에 대입

lambda는 std::function에 대입이 가능합니다. auto 키워드로 타입 추정도 물론 가능합니다.

~~~c
#include <iostream>
#include <functional>

int main() {
    // std::function<void (std::string)>
    auto introduce = [](std::string name) {
        std::cout << "My name is " << name << std::endl;
    };
    
    introduce("Jinsoo Heo");
    
    return 0;
}
~~~

### 2-4. lambda를 함수의 파라미터로 사용

lambda를 std::function에 대입할 수 있다는 것은 그것을 함수의 파라미터로 전달할 수도 있다는 이야기입니다. 하지만 전달되는 함수의 반환형과 그 파라미터의 타입이 항상 같지 않을 수 있으므로 주로 템플릿 함수의 파라미터로 전달됩니다.

~~~c
#include <iostream>
#include <functional>

template<typename T>
void templateFunc(T func) {
    func();
}

int main() {
    // std::function<void (void)>
    auto foo = []() { std::cout << "Hello lambda!" << std::endl; };
    
    templateFunc(foo);
    
    return 0;
}
~~~

### 2-5. lambda를 반환하는 함수

함수가 lambda를 반환하는 것도 가능합니다.

~~~c
#include <iostream>
#include <functional>

std::function<void (void)> getLambda() {
    return []() { std::cout << "Hello lambda!" << std::endl; };
}

int main() {
    auto foo = getLambda();
    
    foo();
    
    return 0;
}
~~~

### 2-6. lambda를 STL container에 저장

~~~c
#include <iostream>
#include <algorithm>
#include <functional>
#include <vector>

int main() {
    std::vector<std::function<void (void)>> funcs;
    
    funcs.push_back([]() { std::cout << "Hello" << std::endl; });
    funcs.push_back([]() { std::cout << "lambda!" << std::endl; });
    
    for(auto& func : funcs) {
        func();
    }
}
~~~


### 2-7. lambda의 반환형 명시
lambda는 값을 반환할 수 있고 그 반환형을 명시할 수 있습니다. C++0x부터 등장한 후행 반환형(trailing return type)을 사용합니다. 또한 함수 포인터나 함수 객체와 다르게 반환형의 추론도 가능합니다.

~~~c
int main() {
    // implicit return type
    auto foo = [] { return 3.14;};
    
    // explicit return type
    auto bar = []() -> float { return 3.14f; };
    
    // warning. double에서 float으로 암시적 형변환.
    float x = foo();
    
    // OK
    float y = bar();
    
    return 0;
}
~~~

## 3. lambda의 캡처
lambda 외부에 정의되어 있는 변수나 상수를 lambda 내부에서 사용하기 위해서 캡처를 사용합니다. 참조(reference)와 복사(copy) 두 방식을 사용할 수 있고, 참조는 &var로 복사는 var로 합니다. 그리고 캡처는 처음에 보았듯이 문법 요소 중 가장 처음에 나오는 []에 기술합니다.


### 3-1. 참조로 캡처

~~~c
#include <array>
#include <algorithm>

int main() {
    std::array<int, 5> numbers = { 1, 2, 3, 4, 5 };
    int sum = 0;
    
    // sum을 참조로 캡처
    std::for_each(numbers.begin(), numbers.end(), [&sum](int& number) { 
        sum += number; 
    });
    
    return 0;
}
~~~

### 3-2. 복사로 캡처

~~~c
#include <iostream>

int main() {
    std::string name = "Jinsoo Heo";
    
    [name]() {
        std::cout << "My name is " << name << std::endl;
    }();
    
    return 0;
}
~~~

복사에선 주의해야할 점이 하나있습니다. 복사로 캡처된 변수는 몸통(body)에서 변경을 할 수 없다는 것이 바로 그것입니다.

~~~c
#include <array>
#include <algorithm>

int main() {
    std::array<int, 5> numbers = { 1, 2, 3, 4, 5 };
    int sum = 0;
    
    // sum을 복사로 캡처
    std::for_each(numbers.begin(), numbers.end(), [sum](int& number) { 
        // 'sum': 변경 불가능한 람다에서 복사 방식 캡처를 수정할 수 없습니다.
        sum += number;
    });
    
    return 0;
}
~~~

lambda의 문법 요소 중 지정자(specifier)라는 것이 있는데 이건 선택 사항입니다. 이 지정자를 이용해서 캡처된 변수를 몸통안에서 어떻게 쓸 것인지 지정할 수 있습니다. 대표적으로 mutable과 constexpr이 있는데, mutable은 복사로 캡처된 변수를 몸통안에서 수정될 수 있도록 허용하는 지정자이고 constexpr은 함수 호출 연산자(function call operator)가 상수 식인 것을 명시하는 지정자입니다. 지정자에 아무 것도 기술하지 않으면 constexpr이 기본값으로 들어갑니다.

~~~c
#include <array>
#include <algorithm>

int main() {
    std::array<int, 5> numbers = { 1, 2, 3, 4, 5 };
    int sum = 0;
    
    // sum을 복사로 캡처. mutable 지정자
    std::for_each(numbers.begin(), numbers.end(), [sum](int& number) mutable { 
        // OK
        sum += number;
    });
    
    return 0;
}
~~~

### 3-3. 여러개의 변수나 상수 캡처
여러개의 변수나 상수를 한번에 캡처할 수 있습니다. []안에 여러 캡처들을 comma(,)로 구분하여 기술하면 됩니다.

~~~c
#include <string>

int main() {
    int x = 0;
    char y = 'J';
    double z = 3.14;
    std::string w = "Jinsoo Heo";
    
    // x, y는 참조로, z, w는 복사로 캡처.
    auto foo = [&x, &y, z, w]() {};
}
~~~

- [a,&b] a를 복사로 캡처, b를 참조로 캡처.
- [this] 현재 객체를 참조로 캡처.
- [&] 몸통에서 쓰이는 모든 변수나 상수를 참조로 캡처하고 현재 객체를 참조로 캡처.
- [=] 몸통에서 쓰이는 모든 변수나 상수를 복사로 캡처하고 현재 객체를 참조로 캡처.
- [] 아무것도 캡처하지 않음.

[&]나 [=]는 lambda가 정의되어있는 함수를 넘어서서 전역(global) 범위까지가 그 캡처 범위입니다. [&, a] 이렇게 하면 모든 변수나 상수를 캡처하고 a만 예외적으로 복사로 캡처하는 것입니다. [=, &b]도 마찬가지로 모든 변수나 상수를 캡처하고 b만 예외적으로 참조로 캡처하는 것이구요. 그런데 [&, &a] 이렇게 쓰는 건 안됩니다. a를 같은 방식으로 2번 캡처하는 것이기 때문이죠.

~~~c
int main() {
    int a, b, c;
    
    // OK
    [&, a, b]() {}();
    // OK
    [=, &c]() {}();
    
    // 기본 캡처 모드가 참조 방식인 경우 '&a'을(를) 사용할 수 없습니다.
    [&, &a]() {}();
    // 기본 캡처 모드가 값 방식인 경우 '&b'이(가) 필요합니다.
    [=, b]() {}();
}
~~~


### 3-4. 전역 변수 캡처

전역 변수를 아래와 같이 캡처하면 오류가 발생합니다.

~~~c
#include <array>
#include <algorithm>

int sum = 0;

int main() {
    std::array<int, 5> numbers = { 1, 2, 3, 4, 5 };
    
    // 전역 변수 sum을 참조로 캡처
    std::for_each(numbers.begin(), numbers.end(), [&sum](int& number) { 
        // 'sum': 람다 캡처 변수는 바깥쪽 함수 범위에 속해야 합니다
        sum += number;
    });
    
    return 0;
}
~~~

전역 변수를 캡처하기 위해서는 기본 캡처 모드(capture-default)를 사용해야 합니다.

~~~c
#include <array>
#include <algorithm>

int sum = 0;

int main() {
    std::array<int, 5> numbers = { 1, 2, 3, 4, 5 };
    
    // 참조 방식의 기본 캡처 모드
    std::for_each(numbers.begin(), numbers.end(), [&](int& number) { 
        // OK
        sum += number;
    });
    
    return 0;
}
~~~

### 3-5. 클래스 멤버 함수 속 lambda
클래스 멤버 함수안에서 lambda를 정의하면 [this]로 현재 객체를 참조로 캡처할 수 있습니다. 이때 lambda는 friend 함수이므로 현재 객체의 private 멤버에도 접근할 수 있습니다.

~~~c
#include <iostream>
#include <string>

class Person {
public:
    Person(std::string name) : name(name) {}
    void introduce() {
        [this]() { std::cout << "My name is " << name << std::endl; }();
    }
private:
    std::string name;
};

int main() {
    Person* devkoriel = new Person("Jinsoo Heo");
    devkoriel->introduce(); // My name is Jinsoo Heo
    
    return 0;
}
~~~

클래스 멤버 함수안에서 정의되는 것뿐만 아니라 lambda 자체가 멤버로 선언될 수도 있습니다. 이럴 경우 기존 멤버 다루듯 하면 됩니다. [this]로 현재 객체를 참조로 캡처할 수 있는 것도 마찬가지입니다.


### 3-6. lambda 재귀

lambda는 기본적으로 함수이기 때문에 재귀도 당연히 가능합니다. 대입된 std::function 함수를 참조로 캡처한 후 몸통에서 호출하면 됩니다.


~~~c
#include <iostream>
#include <functional>

int main() {
    std::function<int (int)> factorial = [&factorial](int x) -> int {
        return x <= 1 ? 1 : x * factorial(x - 1);
    };
    
    std::cout << "factorial(5): " << factorial(5) << std::endl;
}
~~~

팩토리얼 함수를 lambda와 재귀를 통해 구현해보았습니다. 간단한 코드이지만 주의해야할 점이 있습니다. lambda를 대입시킬 함수의 타입을 auto 키워드로 추론하면 안됩니다. 어찌보면 당연한 것이, auto 키워드를 쓰면 컴파일러가 타입을 추론하게 되는데, 타입이 제대로 추론되기도 전에 lambda 몸통에서 그 함수를 재귀적으로 호출하고 있기 때문입니다. lambda 재귀를 할땐 반드시 대입시킬 함수의 타입을 명시하여야 합니다.


## 4. 정리
lambda는 익명 함수(anonymous function)이고 함수 객체를 생성한다.
lambda는 함수 포인터와 함수 객체의 장점을 모두 가지고 있다.
기본 캡처 모드(capture-default), 복사, 참조를 통해 변수나 상수를 캡처할 수 있다.
함수에서 반환할 수도 있고 함수의 파라미터로 전달할 수도 있다.
클래스 멤버 함수안에서 정의되는 lambda는 [this]로 현재 객체를 참조로 캡처할 수 있다.
재귀 호출이 가능하다.