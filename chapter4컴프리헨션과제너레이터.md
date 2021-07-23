# 목차
- [Better way 27. map과 filter 대신 컴프리헨션을 사용하라](#1)
- [Better way 28. 컴프리헨션 내부에 제어 하위식을 세 개 이상 사용하지 말라](#2)
- [Better way 29. 대입식을 사용해 컴프리헨션 안에서 반복 작업을 피하라](#3)
- [Better way 30. 리스트를 반환하기보다는 제너레이터를 사용하라](#4)
- [Better way 31. 인자에 대해 이터레이션알 때는 방어적이 돼라](#5)
- [Better way 32. 긴 리스트 컴프리헨션보다는 제너레이터 식을 사용하라](#6)
- [Better way 33. yield from을 사용해 여러 제너레이터를 합성하라](#7)
- [Better way 34. send로 제너레이터레 데이터를 주입하지 말라](#8)
- [Better way 35. 제너레이터 안에서 throw로 상태를 변화시키지 말라](#9)
- [Better way 36. 이터레이터나 제너레이터를 다룰 때는 itertools를 사용하라](#10)
- [문제](#11)
- [문제풀이](#12)
- [추가자료](#13)

<h1> chapter 4. 컴프리헨션과 제너레이터 </h1>
<blockquote>
  많은 프로그램이 리스트, 딕셔러니의 키/값 쌍, 집합 처리를 중심으로 만들어진다. 파이썬에서는 컴프리헨션이라는 구문을 사용해 이런 타입을
  간결하게 이터레이션하면서 원소로부터 파생되는 데이터 구조를 생성할 수 있다. 컴프리헨션을 사용하면 코드의 가독성을 높일 수 있고 다른 이점도
  얻을 수 있다.
  </blockquote>
 <blockquote>
  컴프리센션 코딩 스타일은 제어레이터를 사용하는 함수로 확장할 수 있다. 제너레이터는 함수가 점진적으로 반환하는 값으로 이뤄지는 스트림을 만들어 준다.
  이터레이터를 사용할 수 있는 곳이라면 어디에서나 제너레이터 함수를 호출한 경과를 사용할 수 있다.
  </blockquote>
  
  
<a name="1"></a>
## Better way 27. map과 filter 대신 컴프리헨션을 사용하라
  
 <a> 파이썬은 다른 시퀀스나 이터러블에서 새 리스트를 만들어내는 간결한 구문을 제공한다. 이런 식을 리스트 컴프리헨션이라고 한다.
     다음은 모든 원소의 제곱을 하는 코드를 컴프리헨션으로 간단하게 표현한 것이다.</a>
     
 ```python
 a = [1,2,3,4,5,6,7,8,9,10]
 
 # 단순 for문을 이용한 코드
 squares = []
 for x in a:
     squares.append(x**2)
 print(squares)
 
 # 컴프리헨션을 이용한 간단한 코드
 squares = [x**2 for x in a]
 print(squares)
 
 # 참고) map으르도 표현가능 하지만 시각적으로 좋지 않다.
 squares = map(lambda x: x**2, a)
 ```
 <a> 단순 for문 이외에도 이중 for문, if를 사용한 구문도 가능하다. </a>
 
 ```python
 # 짝수만 제곱을 하고 싶을 때 (for문과 if문 사용)
 even_squares = [x**2 for x in a if x % 2 == 0]
 
 # 이중 for문을 사용할 때
 matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
 flat = [x for row in matrix for x in row]
 print(flat)
 
 ```
 
 
 <a name="2"> </a>
 <h2>
  Better way 28. 컴프리헨션 내부에 제어 하위식을 세 개 이상 사용하지 말라
  </h2> 
  <a> 컴프리헨션이 편리하다고 하지만, 여러 for문과 if문을 같이 쓰다 보면 가독성이 많이 낮아 질 수 있다. 그러므로 컴프리헨션이 복잡해지면
  여러줄로 나누어 쓰는 것이 더 보기에 좋다 </a>
  
```python 
# 이중 for문에서 3으로 나누어 떨어지고 row의 합이 10 이상일 때 해당 숫자 추출
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
filtered = [[x for x in row if x % 3 == 0] for row in matrix if sum(row) >= 10] # 코드 가독성이 떨어진다.
print(filtered)
```
 
  <a name="3"> </a>
 <h2>
  Better way 29. 대입식을 사용해 컴프리헨션 안에서 반복 작업을 피하라
  </h2> 
  <a> 컴프리헨션에서 같은 계산을 여러 위치에서 공유하는 경우가 흔한데, 대입식을 사용해 반복작업을 피해야 한다. </a>
  
 ```python
stock = {
    '못': 125,
    '나사못': 35,
    '나비너트': 8,
    '와셔': 24,
}

order = ['나사못', '나비너트', '클립']

def get_batches(count, size):
    return count // size

result = {}
for name in order:
    count = stock.get(name, 0)
    batches = get_batches(count, 8)
    if batches:
        result[name] = batches

print(result)

# 왈러스 연산자로 더 짧게 가능하다
found = {name: batches for name in order
         if (batches := get_batches(stock.get(name, 0), 8))}
 ```
 
 
 <a name="4"> </a>
 <h2> Better way 30. 리스트를 반환하기보다는 제너레이터를 사용하라 </h2>
 
 
 ```python
 def index_words(text):
    result = []
    if text:
        result.append(0)
    for index, letter in enumerate(text):
        if letter == ' ':
            result.append(index + 1)
    return result

#
address = '컴퓨터(영어: Computer, 문화어: 콤퓨터, 순화어:전산기)는 진공관'
result = index_words(address)
print(result[:10])
```

<a> 이 코드는 잘 작동하지만 잡음이 많고 핵심을 알아보기 어렵다. 이를 제너레이터를 사용하여 개선해보자 </a>

```python
#
def index_words_iter(text):
    if text:
        yield 0
    for index, letter in enumerate(text):
        if letter == ' ':
            yield index + 1

#
it = index_words_iter(address)
print(next(it))
print(next(it))

#
result = list(index_words_iter(address))
print(result[:10])
```
<a>제너레이터를 사용하면 결과를 리스트에 합쳐서 반환하는 것보다 더 깔끔하다. </a>


<a name="5"> </a>
 
 <h2>
  Better way 31. 인자에 대해 이터레이션알 때는 방어적이 돼라
  </h2> 
  
  <a>입력 인자를 여러 번 이터레이션하는 함수나 메서드를 조심하라. 입력받은 인자가 이터레이터면 함수가 이상하게 작동하거나 결과가 없을 수 있다. </a>
  
  
  ```python
  # my_number.txt는 https://github.com/gilbutITbook/080235/tree/master/Chapter4 참고
def read_visits(data_path):
    with open(data_path) as f:
        for line in f:
            yield int(line)

#
it = read_visits('my_numbers.txt')
percentages = normalize(it)
print(percentages)

#
it = read_visits('my_numbers.txt')
print(list(it))
print(list(it)) # 이미 모든 원소를 다 소진했다
```

<a> 이런 현상이 일어난 이유는 이터레이터가 결과를 단 한번만 만들어내기 때문이다. </a>

```python
def normalize_copy(numbers):
    numbers_copy = list(numbers) # 이터레이터 복사
    total = sum(numbers_copy)
    result = []
    for value in numbers_copy:
        percent = 100 * value / total
        result.append(percent)
    return result

#
it = read_visits('my_numbers.txt')
percentages = normalize_copy(it)
print(percentages)
assert sum(percentages) == 100.0
```

<a> __iter__ 메서드를 제너레이터로 정의하면 쉽게 이터러블 컨테이너 타입을 정의할 수 있다. </a>

```python
#
class ReadVisits:
    def __init__(self, data_path):
        self.data_path = data_path

    def __iter__(self):
        with open(self.data_path) as f:
            for line in f:
                yield int(line)

#
visits = ReadVisits(path)
percentages = normalize(visits)
print(percentages)
assert sum(percentages) == 100.0
```


<a name="6"> </a>
 
 <h2>
  Better way 32. 긴 리스트 컴프리헨션보다는 제너레이터 식을 사용하라
  </h2> 

<a> 입력이크면 메모리를 너무 많이 사용하기 때문에 리스트 컴프리헨션은 문제를 일으킬 수 있다. 하지만 제너레이터 식은 이터레이터처럼 한 번에 원소를 하나씩 출력하기 때문에 메모리 문제를 피할 수 있다. 또한 제너레이터 식이 반환한 이터레이터를 다른 제너레이터 식의 하위 식으로 사용함으로써 제너레이터 식을 서로 합성할 수 있다. 서로 연결된 제너레이터 식은 매우 빠르게 실행되며 메모리도 효율적으로 사용한다.</a>


<a name="7"> </a>

 <h2>
  Better way 33. yield from을 사용해 여러 제너레이터를 합성하라
  </h2> 
  
  <a>yield from 식을 사용하면 여러 내장 제너레이터를 모아서 제너레이터 하나로 합성할 수 있다. 또한 성능도 제너레이터를 사용하는 것보다 좋다.  </a>
  
  ```python
  # 예) 화면의 이미지를 움직이게하는 프로그램 작성
#
def move(period, speed):
    for _ in range(period):
        yield speed

#
def pause(delay):
    for _ in range(delay):
        yield 0

# 단순 yield를 사용한 것
def animate():
    for delta in move(4, 5.0):
        yield delta
    for delta in pause(3):
        yield delta
    for delta in move(2, 3.0):
        yield delta
# 결과
def render(delta):
    print(f'Delta: {delta:.1f}')
    # 화면에서 이미지를 이동시킨다

def run(func):
    for delta in func():
        render(delta)

run(animate)


# yield from 을 사용한 것
def animate_composed():
    yield from move(4, 5.0)
    yield from pause(3)
    yield from move(2, 3.0)


run(animate_composed)


  ```


<a name="8"> </a>

 <h2>
  Better way 34. send로 제너레이터에 데이터를 주입하지 말라
  </h2> 

<a>  send 메서드를 사용해 데이터를 제너레이터에 주입할 수 있지만, send와 yield from 식을 함께 사용하면 제너레이터의 출력에 None이 불쑥불쑥 나타나는 이외의 결과를 얻을 수도 있다. </a>

<a>  합성할 제너레이터들의 입력으로 이터레이터를 전달하는 방식이 send를 사용하는 방식보다 더 낫다. send는 가급적 사용하지 말라. </a>

```python
# send 사용 예

it = iter(my_generator())
output = it.send(None)     # 첫 번째 제너레이터 출력을 얻는다
print(f'출력값 = {output}')

try:
    it.send('안녕!')    # 값을 제너레이터에 넣는다
except StopIteration:
    pass
```

<a name="9"> </a>

 <h2>
  Better way 35. 제너레이터 안에서 throw로 상태를 변화시키지 말라
  </h2> 
  
 <a> throw 메서드를 사용하면 제너레이터가 마지막으로 실행한 yield 식의 위치에서 예외를 다시 발생시킬 수 있다. 하지만 throw 사용시 가독성이 나빠진다. 예외를 잡아내고 다시 발생기키는 데 준비 코드가 필요하며 내포단계가 깊어지기 때문이다. </a>
 
 <a> 제너레이터에서 예외적인 동작을 제공하는 더 나은 방법은 __iter__메서드를 구현하는 클래스를 사용하면서 예외적인 경우에 상태를 전이시키는 것이다. </a> 
 
 ```python
 # throw 간단 예제
 
 def my_generator():
    yield 1
    try:
        yield 2
    except MyError:
        print('MyError 발생!')
    else:
        yield 3
    yield 4

it = my_generator()
print(next(it))  # 1을 내놓음
print(next(it))  # 2를 내놓음
print(it.throw(MyError('test error')))

 ```
  
```python
# throw와 __iter__ 메소드 비교

# 작성하는 프로그램에 간헐적으로 재설정할 수 있는 타이머 

#1. throw를 이용한 코드
class Reset(Exception):
    pass

def timer(period):
    current = period
    while current:
        current -= 1
        try:
            yield current
        except Reset:
            current = period

#
RESETS = [
    False, False, False, True, False, True, False,
    False, False, False, False, False, False, False]

def check_for_reset():
    # 외부 이벤트를 폴링한다
    return RESETS.pop(0)

def announce(remaining):
    print(f'{remaining} 틱 남음')

def run():
    it = timer(4)
    while True:
        try:
            if check_for_reset():
                current = it.throw(Reset())
            else:
                current = next(it)
        except StopIteration:
            break
        else:
            announce(current)

# 2. __iter__이용한 코드
class Timer:
    def __init__(self, period):
        self.current = period
        self.period = period

    def reset(self):
        self.current = self.period

    def __iter__(self):
        while self.current:
            self.current -= 1
            yield self.current

#
def run():
    timer = Timer(4)
    for current in timer:
        if check_for_reset():
            timer.reset()
        announce(current)
        
# __iter__를 사용한 코드가 더 가독성이 좋다.

```
  
<a name="10"> </a>

 <h2>
  Better way 36. 이터레이터나 제너레이터를 다룰 때는 itertools를 사용하라
  </h2> 
  
  <a> itertools 내장 모듈에는 이터레이터를 조직화하거나 사용할 때 쓸모있는 여러함수가 들어있다. </a>
  
  ```python 
  # 대표적인 itertools 함수들
  
#
import itertools

# 합치기
it = itertools.chain([1, 2, 3], [4, 5, 6])
print(list(it))

# 반복
it = itertools.repeat('안녕', 3)
print(list(it))

# 특정원소 반복
it = itertools.cycle([1, 2])
result = [next(it) for _ in range (10)]
print(result)

# 병렬적으로 만들고 싶을때
it1, it2, it3 = itertools.tee(['하나', '둘'], 3)
print(list(it1))
print(list(it2))
print(list(it3))

# zip함수의 변종으로 한쪽이 원소가 부족한 경우 빈칸을 채워준다. fillvalue
keys = ['하나', '둘', '셋']
values = [1, 2]

normal = list(zip(keys, values))
print('zip:', normal)

it = itertools.zip_longest(keys, values, fillvalue='없음')
longest = list(it)
print('zip_longest:', longest)

# 더 많은 함수 알아보기
 help(itertools)
  ```

<a name="11"> </a>

 <h2>
  연습문제
  </h2> 

```python
# 문제 1. 리스트를 반환하지말고 제너레이터로 사용하라
def get_even_number(num_list:list):
    return_list = []
    for num in num_list:
        if num % 2 == 0:
            return_list.append(num)
        else:
            pass

    return return_list


# 문제 2. animate() 함수를 yield from 문을 사용하여 개선시켜라
# animate() 함수를 yield from 문을 사용하여 개선시켜라
def animate():
    for delta in move(4, 5.0):
        yield delta
    for delta in pause(3):
        yield delta
    for delta in move(2, 3.0):
        yield delta

def render(delta):
    print(f'Delta: {delta:.1f}')
    # 화면에서 이미지를 이동시킨다

def run(func):
    for delta in func():
        render(delta)

run(animate_composed)


# 문제 3. 다음 for문을 컴프리헨션으로 바꿔보자

q1 = list(range(1,11))
ans = []
for i in q1:
    if i % 2 == 0:
        ans.append(i**2)   # 짝수만을 가져와서 제곱하는 for 문
print(ans)


# 문제 4. 다음 함수를 yield from을 활용해서 더 간단하게 바꿔보자

def quiz2():
    ans2 = []
    for i in range(0,7):
        ans2.append(i)
    for i in range(3,9):
        ans2.append(i)
    return ans2
quiz2()


# 문제 5.6.
food = ['rice_cake', 'chicken', 'salad']

# 음식의 칼로리를 구하는 함수
def get_food_calrories(food):
    result = []
    for f in food:
        # 칼로리를 구하는 함수라고 가정하자
        calories = len(f)*77/3
        result.append(calories)
    return result

# 문제 5. get_food_calrories함수를 get_food_calrories_ gen라는 제너레이터로 함수로 바꿔라.
# 문제 6. get_food_calrories_gen함수를 두 번 호출하면 어떤 결과가 나올고 그 이유는?
it = get_food_calrories_gen(food)
print(list(it)) 
print(list(it)) # 이때 결과값은?

# 문제 7. 이터레이터 프로토콜을 구현해 새로운 컨테이너 클래스를 작성해 2의 문제를 해결해보라
class FoodIter:
    def __init__(self,food_list):
        self.plates = food_list 
    def __iter__(self):
        #코드 작성 
food = FoodIter(food) ################### 수정!!!!
cal=get_food_calrories(food)
print(cal) # 제대로 출력이 된다!
print(cal) # 제대로 출력이 된다!

```


