회사 업무에 도움이 되는 pcre_regex 정규표현식 정리를 합니다.
regular expressions 검증을 위한 유용한 사이트 3개
[Regexr]([https://regexr.com/)     
[regex101]([https://regex101.com/)     
[regexper](https://regexper.com/)      
```
PCRE는 "Perl-Compatible Regular Expressions"의 약어로,
강력한 정규 표현식을 지원하는 프로그래밍 언어인 Perl의 기능을 다른 프로그래밍 언어나 도구에서도 사용할 수 있게 해주는 라이브러리를 의미합니다.
정규 표현식은 특정 패턴을 표현하는데 사용되며, 텍스트 처리, 검색, 추출, 치환 등 다양한 작업에 유용합니다. 
```
snort rule 에서 PCRE 를 사용하고 확인하는데요
복잡한 규칙이 많이 있어 검증을 하기 위해 특정 사이트들을 주로 이용합니다.

정규표현식 자체가 CRLF를 허용하지 않아서 한줄에 다적어야 한다.
```
CRLF 란CR :
Carriage Return (\r) : 현재 라인에서 커서의 위치를 가장 앞으로 옮기는 동작을 의미합니다
LF : Line Feed (\n) :  약자로 커서의 위치는 그대로 두고 종이를 한 라인 위로 올리는 동작을 의미합니다.

이 방식(CR + LF)은 타자기 이후 컴퓨터에서도 줄바꿈을 의미할 때도 사용되었으나,
줄바꿈을 할 때 굳이 2 byte 를 사용할 필요가 없기에 메모리/Storage 절약을 위해 CR 혹은 LF 만 사용하기도 하였다.
대표적으로 Microsoft 사의 Windows 는 CRLF (\r\n) 을 기본으로 사용하는
반면  Unix/Linux 에서는 LF (\n) 만으로 줄바꿈을 하고 있다. (Mac 의 초기 버전, 9 버전 이하는 CR (\r) 을 줄바꿈으로 사용)
좀 더 명확히 얘기하자면 해당 시스템에서 사용하는 default (기본) 방식이 그렇다는 것이지 반드시 해당 시스템에서는 해당 방식을 사용해야한다는 것은 아니다.
아래와 같이 application에서 적절히 처리할 수 있다. 
```
주로 프로그래밍 언어나 문서편집기 등에서 쓰이는 것 같은데 회사에서 snort 룰 설정에 정규표현식하는 업무가 필수적이라 꼭 알아둬야한다..
```
  <((https?|ftp|file)://|(docs|www|ftp).)[-a-zA-Z0-9+&@#/%?=~_|$!:,.;]*[a-zA-Z0-9+&@#/%=~_|$]> 14
```
## POSIX와 PCRE 로 구분

Regular Expression 분류

regex는 크게 2가지로 분류할 수 있다. POSIX 표준과 PCRE

- POSIX (Portable Operation System Interface)

UNIX 시스템 기반의 regex이다. 이녀석이 표준으로 지정되어 있음.

간단하고 배우기 쉬우며, 처리속도가 빠르지만 패턴이 복잡해지면 성능저하가 일어난다.

- PCRE (PERL Compatible Regular Expression)

POSIX에서 확장된 Perl 방식의 정규표현식이다.

매우 빠른 속도에 다양한 표현식을 제공한다.

대부분의 언어들이 PCRE를 지원하므로, 실무에서는 PCRE를 사용하는 것이 적합하다.

(Snort도 PCRE를 사용한다.)

- 그 외 Regular Expression

Vim, Emacs 등에서 사용되는 정규표현식도 별도로 존재한다.

정규표현식에서 사용되는 여러가지 문자들에 대해 알아봅시다.

#### 1. 반복 메타문자
![image](https://github.com/user-attachments/assets/3f36bf67-1dfa-43f1-ae7a-cf169222300e)
- https?

'?'앞의 하나의 문자가 없거나 한번만 반복하는 것을 찾는다.

즉, http 나 https 를 찾는 것이다. '?' 앞에 하나만 본다는거 까묵지말자 

- kim+

'+'앞의 문자 하나가 한번 이상 반복되는 것이 매칭된다. 

즉, kim+니까 kim, kimm, kimmm 이 매칭된다.

- dong*

'*'앞의 문자 하나가 없거나 한번 이상 반복되는 것이 매칭된다. 

'+'는 하나 이상은 있어야 했고, '*'는 이거 앞에 문자 하나가 없더라도 찾는다.

즉, dong* 해놓으면 dong, dongg은 물론 don 까지 매칭된다.

- {n}, {n, p}, {n, }

괄호 안에 콤마를 기준으로 {최소 반복횟수, 최대 반복횟수} 이다.

위 문자들과 동일하게 바로 앞에 한글자를 반복하는 횟수를 지정한다.

#### 2. 매칭 메타문자
![image](https://github.com/user-attachments/assets/b4344b18-bd3b-42ed-9c8c-d4fb9fe755c4)

- ^abc

'^' 다음에 오는 문자열로 시작되는 것들을 매칭시킨다. abcd, abcdde ..

- abc$

'$' 이전에 오는 문자열로 끝나는 것들을 매칭시킨다. edfabc, dddabc ...

즉, "^$" 라고 돼있으면 빈줄을 의미한다.

- .a

라고 돼있으면 . 자리에 오는 문자가 들어가는 뭐든 간에 매칭된다. babc, dda ..

단, NewLine은 제외된다.

- [ xyz ]

x, y, z 중 한글자와 매칭

- [ a-z ]

a에서 z사이에 있는 한문자와 매칭

- [ B-Pk-y ]

B에서 P까지 또는 k에서 y사이 한문자와 매칭

- [ a-z0-9 ]

소문자나 숫자 중의 한문자와 매칭

- [ ^b-d ]

b에서 d사이 문자를 제외한 모든 문자를 나타냄 

- (d|f)ood

우리가 기존에 알던 그대로 d또는 f가 들어간 것과 매칭한다.

- (f . .) (b . .)

매치되는 하나의 문자열 즉, 객체를 구분지어준다.

#### 3. 이스케이프 기호
![image](https://github.com/user-attachments/assets/705b70fd-3bd4-45c8-8c43-facdcdfd7168)

이스케이프 기호는 적혀있는대로.. 딱히 설명할게 없다.

우리가 늘 사용하던 \n, \t 등이 여기에 해당한다.

#### 4. 탐색 플래그
![image](https://github.com/user-attachments/assets/6a587027-ecb7-4d8f-8078-c87d28128e2e)
추가로 x는 공백을 말함.    

m은 쉽게 말해 다중행
  
ps. 쉬는 시간에 정규표현식으로 바꾸는 연습해야겠다. siem 장비로 여러 장비들의 이벤트를 모아서 파싱해서 필요한 정보를 얻는데
자기가 미리 설정한 (맞춤설정) 기능으로 파싱하면 업무에 편하지않을까..
