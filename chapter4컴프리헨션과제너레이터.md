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
- [문제](#8)
- [문제풀이](#9)
- [추가자료](#10)

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




