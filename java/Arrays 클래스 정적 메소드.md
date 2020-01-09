# Arrays 클래스 정적 메소드



### 1. stream(array) 메서드

```java
public String solution(int[] numbers) {
    StringBuilder sb = new StringBuilder();
    List<String> collect = Arrays.stream(numbers) // Instream
    	.mapToObj(String::valueOf) // stream<String>
        .sorted(new BigComparator()) // stream<String>
        .collect(Collectors.toList()); // List<String>

}
```

int[] -> Instream 으로의 변환을 해준다.
반환타입이 Instream이기 때문에 mapToObj 등을 이용해서 내가 원하는 타입의 stream으로 변환해줄 것.



### 2. toString(array) 메서드

```java
int[] solution = {3,1,5,6};
System.out.println(Arrays.toString(solution));
```

Arrays.toString() 을 이용하면 sout 을 이용해서 array를 간단하게 출력하고 싶을 때 사용할 수 있다.



### 3. sort(array) 메서드

```java
int[] integer = {1,2,3,6,5};
Arrays.sort(integer);
```

primitive 타입을 간단하게 sorting 하고 싶다면 sort(Array) 메서드를 사용하면 된다.

```java
Alphabet[] alphabets = {new Alphabet(1, 'a'), new Alphabet(2, 'b')};
Arrays.sort(alphabets);
Arrays.sort(alphabets, Comparator.comparingInt(o -> o.number));
```

Object를 sorting 하고 싶을 때 유의할 점이 있다.
Integer, Boolean, Double 같은 Wrapper 클래스라면 Arrays.sort()를 사용해도 되지만, 위와 같이 멤버 필드를 정의한 새로운 객체의 경우에는 Comparator를 새로 정의해야한 후에 2번째 인자로 넣어주어야한다.



### 4. copyOfRange(array) copyOf(array) 배열 깊은 복사

```java
int[] array = {1, 5, 2, 6, 3, 7, 4};
int[] array2 = Arrays.copyOf(array, 4); // {1,5,2,6}
int[] array3 = Arrays.copyOfRange(array, 3, 5); // {6,3}
```

copyOf 는 original 배열과, 새로 생성할 array의 새로운 길이를 인자로 넣어준다. 만약 인자 값이 기존의 배열의 크기보다 작을경우는 index 0부터 해당 길이까지 자른다.

copyOfRange는 original 배열에서 내가 지정한 index 만큼을 자른다. (object[] originalArray, int from, int to)로 인자 타입이 지정되어있는데, from에 속하는 인덱스부터 to 이전의 인덱스 까지를 자른다.
즉 **[from, to)** 로 지정한 인덱스 만큼 배열을 자른다.

