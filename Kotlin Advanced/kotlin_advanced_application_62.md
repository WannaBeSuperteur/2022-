## 요구사항 분석
문자열 ```string```이 표현 형식 ```expression```과 일치하는지 검사하는 함수 ```isMatched(string, expression)```을 작성하시오.

```expression```은 다음을 concatenate하여 연결한 형태이다.
* ```a``` : 문자 a 1개를 의미한다.
* ```a+``` : 문자 a 1개 이상을 의미한다.
* ```a*``` : 문자 a 0개 이상을 의미한다.

예를 들어 ```aaabbb```는 표현 형식 ```a*bb+```과 일치하므로, ```isMatched("aaabbb", "a*bb+")```의 값은 true이다.
