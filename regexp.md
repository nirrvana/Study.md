# 정규표현식은 강력하다

## Regular Expressions

Testbuilder, Twittler 과제를 진행해오며 일부 코드에 정규표현식을 사용해 보았다.
정규표현식은 짧지만, 그 효과가 강력했다.
코드의 양을 줄여 효율성과 생산성 증대를 기대할 수 있었다.
<br/>
- **Twittler**

트위틀러 과제를 진행하며 textarea에 유저이름과 내용을 입력하지 않을 경우, submit 되지 않고 경고창이 뜨도록 조건을 설정해 주었는데 이 때, 정규표현식을 사용하였다.

만일 정규표현식을 사용하지 않았다면 코드를 어떻게 작성할 수 있었을까?

    let usernameField = document.querySelectorAll('.twit-form__field')[0];
    if (usernameField === '') {
    ...
이런 식으로, 유저이름이 빈 string일 경우 submit이 되지 못하도록 알고리즘을 짜 볼 수 있을 것이다.
<br/>

    ''    // 빈 스트링 1번
    ' '   // 빈 스트링 2번
    '   ' // 빈 스트링 3번
    ...   // 빈 스트링 n번
유저이름이 빈 스트링 n번인 경우 submit 되지 않도록 for문을 사용하여 알고리즘을 짜볼 수 있을 것이다.
가능은 하나, 너무 길어진다.
<br/>

정규표현식을 사용하면 아래와 같이 코드를 간단하게 작성하여 원하는 동작을 구현할 수 있다.

    let usernameField = document.querySelectorAll('.twit-form__field')[0];
      if (!/\S/.test(usernameField.value)) {
        alert('유저이름을 입력해 주세요');
      }
유저이름이 '공백이 아닌 문자'를 포함하고 있는지를 test하여 결과값이 false인 경우 '유저이름을 입력해 주세요' 라는 경고창이 뜨는 동작을 구현할 수 있게 된다.
(textarea가 '공백이 아닌 문자'를 포함하는지 test하여 true값이 나와야 submit할 수 있다.)

```\S``` :  공백이 아닌 문자
위의 경우 test method가 사용되었는데, 사용법은 아래와 같다.  
<br/>


> Test method

문법 : ```정규표현식.test(정규표현식을 적용할 문자열)```
결과 : boolean값

    var str = "hello world";
    var result = /^hello/.test(str);
    console.log(result) // true
<br/>
<br/>

- **Testbuilder**

cardNumber 앞의 두 자리(prefix)가 어떠한 경우에 어떠한 이름이 나오게 하려고 한다. prefix를 어떻게 지정할 수 있을까?

    // 방법1
    let prefix = cardNumber[0] + cardNumber[1];
    if (prefix === 38 || prefix === 39) {
    ...
    
    // 방법2 
    let prefix = cardNumber.slice(0, 2);
    if (prefix === 38 || prefix === 39) {
    ...

<br/>
과제를 진행해 나가면서, prefix의 길이가 달라지는 경우 그에 대한 prefix 초기화를 다시 해주어야 했다.

정규 표현식을 사용하면 문제는 간단해진다.

    if (/^3[89]/.test(cardNumber)) {

<br/>
```^``` :  입력된 문자열의 시작 부분에 대응
```[89]``` : 8,9 중 하나를 의미
```[1-5]``` : 1,2,3,4,5 중 하나를 의미 = [12345]
<br/>

이와 같이 정규표현식을 사용하면 prefix라는 변수를 따로 지정하지 않아도 되고, prefix의 길이가 달라지는 경우에도 대응이 가능하다.

정규표현식은 짧지만, 강력하다!
정규표현식 공부할 수 있는 사이트
<br/>
[http://zvon.org/](http://zvon.org/)