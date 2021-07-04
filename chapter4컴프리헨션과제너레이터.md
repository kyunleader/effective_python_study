<h1> 목차 </h1>
<ul>
  <li>Better way 27. map과 filter 대신 컴프리헨션을 사용하라
  </li>
  <li>Better way 28. 컴프리헨션 내부에 제어 하위식을 세 개 이상 사용하지 말라
  </li>
  <li>Better way 29. 대입식을 사용해 컴프리헨션 안에서 반복 작업을 피하라
  </li>
  <li>Better way 30. 리스트를 반환하기보다는 제너레이터를 사용하라
  </li>
  <li>Better way 31. 인자에 대해 이터레이션알 때는 방어적이 돼라
  </li>
  <li>Better way 32. 긴 리스트 컴프리헨션보다는 제너레이터 식을 사용하라
  </li>
  <li>Better way 33. yield from을 사용해 여러 제너레이터를 합성하라
  </li>
  <li>Better way 34. send로 제너레이터레 데이터를 주입하지 말라
  </li>
  <li>Better way 35. 제너레이터 안에서 throw로 상태를 변화시키지 말라
  </li>
  <li>Better way 36. 이터레이터나 제너레이터를 다룰 때는 itertools를 사용하라
  </li>    
  <li>문제
  </li>   
  <li>문제풀이
  </li>   
  <li>추가자료
  </li>   
</ul>

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
  
  
<h2>
  Better way 27. map과 filter 대신 컴프리헨션을 사용하라
  </h2>
  
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
 
 
 <h2>
  Better way 29. 대입식을 사용해 컴프리헨션 안에서 반복 작업을 피하라
  </h2> 
 

 
 
