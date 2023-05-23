# 1. 정규 표현식 (Regular Expression)
- 정규 표현식에 대해 조사하고, 본인의 깃허브에 public 프로젝트로 등록하여 정리하시오.
- 정규 표현식이 왜 필요한지, 파이썬에서의 기본 사용법, 코드 예제가 필수로 포함되어야 함

## 정규 표현식이란?
-  정규표현식(regular expression)은 일종의 문자를 표현하는 공식으로, 특정 규칙이 있는 문자열 집합을 추출할 때 자주 사용되는 기법이다.
- 컴퓨터 과학의 정규 언어로부터 유래하였으나 구현체에 따라서 정규 언어보다 더 넓은 언어를 표현할 수 있는 경우도 있으며, 심지어 정규 표현식 자체의 문법도 여러 가지 존재하고 있다. 현재 많은 프로그래밍 언어, 텍스트 처리 프로그램, 고급 텍스트 편집기 등이 정규 표현식 기능을 제공한다. 일부는 펄, 자바스크립트, 루비, Tcl처럼 문법에 내장되어 있는 반면 닷넷 언어, 자바, 파이썬, POSIX C, C++ (C++11 이후)에서는 표준 라이브러리를 통해 제공한다. 그 밖의 대부분의 언어들은 별도의 라이브러리를 통해 정규 표현식을 제공한다.

<br/>

### 정규 표현식 표현방법
-> 정규표현식은 표준인 POSIX의 정규표현식과 POSIX 정규표현식에서 확장된 Perl방식의 PCRE가 대표적이며, 이외에도 수많은 정규표현식이 존재하며 정규표현식 간에는 약간의 차이점이 있으나 거의 비슷하다. 정규표현식에서 사용하는 기호를 Meta 문자라고 한다. Meta 문자는 표현식 내부에서 특정한 의미를 갖는 문자를 말한다.

<br/>

### **정규표현식 기본 문법**
정규표현식 기분 문법은 크게 세 가지 정도로 나눌 수 있다.

**1. 패턴 그대로를 매칭하는 경우** : 편집기에서 “찾기” 기능을 통해서 특정 단어를 찾는 것 처럼, 단어 그대로를 패턴으로 사용하여 매치되는 영역을 찾는다.<br/>
**2. 메타문자 및 수량 한정자를 적용하는 경우** : 정규식 패턴에 쓰이는 문자중에는 특별한 의미를 가지는 메타 문자들이 있는데, 이들을 사용하여 보다 폭넓은 패턴에 매치할 수 있다.<br/>
**3. 그룹 및 look around 기능을 사용하는 경우** : 제법 고급 정규식이라 할 수 있는 부분으로, 패턴의 일부를 그룹으로 묶거나, 특정 패턴의 앞 뒤로 다른 패턴이 오는 조건을 더하는 경우이다.<br/>


<br/>

### 메타문자
: 메타문자는 문자를 설명하기 위한 문자로, 문자의 구성을 설명하기 위해 원래의 의미가 아닌 다른 의미로 쓰이는 문자를 말한다.
- 정규표현식에서는 위와 같은 메타문자를 사용다.

> 💡메타문자 종류 <br/>
^ : 문자열의 처음을 나타낸다. <br/>
$ : 문자열의 끝을 나타낸다. <br/>
. : 임의의 한 문자를 나타낸다. <br/>
*: 바로 앞의 문자가 없거나 하나 이상이다. <br/>
+: 바로 앞의 문자가 하나 이상이다. <br/>
? : 앞의 문자가 없거나 하나이다. <br/>
[ ] : 한 문자를 가리키고 묶음 안의 내용은 가리키는 문자의 범위를 나타낸다.<br/>
{ } : 앞에 있는 문자의 개수를 나타내고 묶음 안에서 ','는 문자 개수의 범위를 나타낼 때 쓴다.<br/>
( ) : 괄호 안의 문자열은 하나로 묶어 취급한다.<br/>
| : 또는(or)의 뜻으로 선택문에 쓰인다.<br/>
 \ : 메타 문자의 성질을 없앨 때 붙인다.<br/>
 
 ### 정규 표현식 (Regular Expression)
![](https://velog.velcdn.com/images/hllee/post/0c8df363-1a7e-4fd1-acc2-503711c7ec33/image.png)

 <br/>
 
 #### 일반적인 조건문을 사용한 경우
 ```java
//text에서 어떤 tag가 있는지 모조리 뽑아내서 list에 담아 반환   
private List<String> tagPicker(String text) {   
       
    List<String> tagList = new ArrayList<String>();   
    int textLength = text.length();   
           
    for(int i=0; i<textLength; i++){   
        StringBuilder sb = new StringBuilder();   
            if(text.charAt(i)=='#'){   
                for(int j=i+1; j<textLength; j++){   
                    if(text.charAt(j)=='\n' || text.charAt(j)==' ' || text.charAt(j)=='#' || text.charAt(j)=='%'   
                        || text.charAt(j)=='.' || text.charAt(j)==',' || j==(textLength-1) ){   
                        if(sb.length()!=0){   
                            if(j==(textLength-1))   
                                sb.append(text.charAt(j));   
                                tagList.add(sb.toString());   
                        }   
                        i=j-1;   
                        break;   
                    } else {   
                        sb.append(text.charAt(j));   
                    }   
                    LOGGER.debug("TAG : " + sb.toString());   
                }   
            }   
        }   
    return tagList; 
}
```
 
 #### 정규표현식을 사용한 경우
 ```java
//text에서 어떤 tag가 있는지 모조리 뽑아내서 list에 담아 반환 
private List<String> getTagListFromText(String text) { 

    List<String> tagList = new ArrayList<String>(); 
     
    Pattern p = Pattern.compile("#([a-zA-Z0-9가-힣]+)");
    Matcher mc = p.matcher(text); 
     
    while(mc.find()){ 
        tagList.add(mc.group(1));  
    } 
    return tagList; 
}
```
<br/>

### 정규표현식을 사용하는 이유
만약 문자열 안에 정수만 추출하고 싶다면 문자열에서 제공하는 함수만으로는 한계가 있다. 이럴 때, 정규 표현식을 사용하여 복잡하고 특정한 규칙을 가진 문자열을 처리하는데 더욱 간결하고 빠르게 구현할 수 있다.

 
 출처 : https://soooprmx.com/%EC%A0%95%EA%B7%9C%ED%91%9C%ED%98%84%EC%8B%9D%EC%9D%98-%EA%B0%9C%EB%85%90%EA%B3%BC-%EA%B8%B0%EC%B4%88-%EB%AC%B8%EB%B2%95/, https://jsj0903.tistory.com/7
 
 ------------------------------------------------------
 ## 파이썬에서의 기본 사용법
 Python에서 정규식 처리 모듈은 re 로, re를 사용하기 위해서는 import re를 선언하고, re 내부 함수와 pattern을 정의하여 호출한다. 
>  **import re**

### 정규식 re 함수
![](https://velog.velcdn.com/images/hllee/post/83a879ab-eb24-4d1e-bf51-f50e90fe2d01/image.png)


#### match(패턴, 문자열, 플래그)
match()는 문자열의 처음부터 시작해서 작성한 패턴이 일치하는지 확한다.
```python
import re

print(re.match('a', 'ab'))
print(re.match('a', 'ab'))
print(re.match('a', 'bba'))
print(re.match('a', 'ba'))

# <re.Match object; span=(0, 1), match='a'>
# <re.Match object; span=(0, 1), match='a'>
# None
# None
```

#### search(패턴, 문자열, 플래그)
search()는 match()와 유사하지만 패턴이 문자열의 처음부터 일치하지 않아도 다된다.
```python
import re


print(re.search('a', 'ab'))
print(re.search('a', 'ab'))
print(re.search('a', 'bba'))
print(re.search('a', 'ba'))

# <re.Match object; span=(0, 1), match='a'>
# <re.Match object; span=(0, 1), match='a'>
# <re.Match object; span=(2, 3), match='a'>
# <re.Match object; span=(1, 2), match='a'>
```

#### findall(패턴, 문자열, 플래그)
findall()은 문자열 안에 패턴에 맞는 케이스를 전부 찾아서 리스트로 반환한다. 
```python
import re


print(re.findall('a', 'a'))
print(re.findall('a', 'aba'))
print(re.findall('a', 'baa'))
print(re.findall('aaa', 'aaaaa'))
print(re.findall('aaa', 'aaaaaa'))
print(re.findall('\d', '숫자123이 이렇게56 있다8'))
print(re.findall('\d+', '숫자123이 이렇게56 있다8'))


# ['a']
# ['a', 'a']
# ['a', 'a']
# ['aaa']
# ['aaa', 'aaa']
# ['1', '2', '3', '5', '6', '8']
# ['123', '56', '8']
```

#### finditer(패턴, 문자열, 플래그)
findall()과 유사하지만 패턴에 맞는 문자열의 리스트가 아닌 iterator 형식으로 반환한다.
```python
import re

re_iter = re.finditer('a', 'baa')
for s in re_iter:
    print(s)


# <re.Match object; span=(1, 2), match='a'>
# <re.Match object; span=(2, 3), match='a'>
```

#### fullmatch(패턴, 문자열, 플래그)
fullmatch()는 문자열에 시작과 끝이 정확하게 패턴과 일치할 때 반환한다. match()는 처음부터 패턴에 맞으면 반환을 하지만 해당 함수는 시작과 끝이 정확하게 일치해야 한한다.
```python
import re

print(re.fullmatch('a', 'a'))
print(re.fullmatch('a', 'aaa'))
print(re.fullmatch('a', 'ab'))
print(re.fullmatch('a', 'ba'))
print(re.fullmatch('a', 'baa'))


# <re.Match object; span=(0, 1), match='a'>
# None
# None
# None
# None
```

#### split(패턴, 문자열, 최대 split 수, 플래그)
split은 문자열에서 패턴이 맞으면 이를 기점으로 리스트로 쪼개는 함수입니다. 만약 3번째 인자(최대 split 수)를 지정하면 문자열을 지정한 수 만큼 쪼개고 그 수가 도달하면 쪼개지 않는다.
```python
import re

print(re.split('a', 'abaabca'))
print(re.split('a', 'abaabca', 2))


# ['', 'b', '', 'bc', '']
# ['', 'b', 'abca']

```

#### sub(패턴, 교체할 문자열, 문자열, 최대 교체 수, 플래그)
sub는 문자열에 맞는 패턴을 2번째 인자(교체할 문자열)로 교체한한다. split의 최대 split 수와 동일하게 최대 교체 수를 지정하면 문자열에 맞는 패턴을 교체할 문자열로 교체하고 그 수가 도달하면 더이상 교체하지 않는다.
```python
import re


print(re.sub('a', 'z', 'ab'))
print(re.sub('a', 'zxc', 'ab'))
print(re.sub('a', 'z', 'aaaab'))
print(re.sub('a', 'z', 'aaaab', 1))


# zb
# zxcb
# zzzzb
# zaaab
```

#### subn(패턴, 교체할 문자열, 문자열, 최대 교체 수, 플래그)
sub()와 동작은 동일하지만 반환 결과가 결과 (문자열, 매칭횟수) 형태로 반환된된다.
```python
import re


print(re.subn('a', 'z', 'ab'))
print(re.subn('a', 'zxc', 'ab'))
print(re.subn('a', 'z', 'aaaab'))
print(re.subn('a', 'z', 'aaaab', 1))

# ('zb', 1)
# ('zxcb', 1)
# ('zzzzb', 4)
# ('zaaab', 1)
```

#### compile(패턴, 플래그)
만약 패턴과 플래그가 동일한 정규식을 여러번 사용하려면 compile()를 사용하여 지정한 다음, 위의 함수들을 사용할 수 있다.
```python
import re

c = re.compile('a')

print(c.sub('zxc', 'abcdefg'))
print(c.search('vcxdfsa'))


# zxcbcdefg
# <re.Match object; span=(6, 7), match='a'>
```

#### purge()
위 complie()로 만들어 놓은 객체는 캐시에 보통 100개까지 저장된다고 알려져 있으며 그 수를 넘어가면 초기화 된다. purge()를 호출하면 100개가 넘어가지 않아도 캐시를 초기화 하는 함수이다.
```python
import re


re.purge()
```

#### escape(패턴)
escape()는 패턴을 입력 받으면 특수문자들에 이스케이프(백슬래쉬) 처리를 한 다음 반환한한다.
```python
import re


print(re.escape('(\d)'))


# \(\\d\)
```

#### match object method()
findall()를 제외하고 모든 함수들의 반환은 match object로 반환된다. match object에서는 group(), start(), end() 등과 같이 찾은 패턴이 문자열의 위치나 매칭 문자열을 반환하는 함수를 제공한다.

group(), start(), end(), span()
예를 들어 search()로 패턴에 맞는 문자열을 찾았다 하면 group() 메서드를 통해 패턴에 맞는 문자열을 추출할 수 있고, start()를 사용해 문자열에서 어디부터 패턴에 맞는 문자가 시작했는지, end()를 통해 어디까지인지, span()으로 어디부터 어디까지인지 확인할 수 있다.
```python
import re

result = re.search('aa', 'baab')
print(result.group())
print(result.start())
print(result.end())
print(result.span())


# aa
# 1
# 3
# (1, 3)
```

#### groups(), group(int)
만약 위와 같이 단순한 형태가 아닌, 소괄호 ()를 사용해 패턴을 묶어 찾는다면 아래와 같이 groups()와 group(int)를 사용할 수 있다.
```python
import re

result = re.match('(\d{2})-(\d{3,4})-(\d{4})', '02-123-1234')
print(result.groups())
print(result.group())
print(result.group(0))
print(result.group(1))
print(result.group(2))

# ('02', '123', '1234')
# 02-123-1234
# 02-123-1234
# 02
# 123
```

#### groupdict()
groupdict()를 사용하려면 패턴에 맞는 결과에 이름을 주어야만 합니다. 패턴에 이름을 주려면 (?P<이름>) 형식이 되어야만 한다. 여기서도 소괄호가 존재하지 않으면 에러가 발생한다.
```python
import re

result = re.match('(?P<front>\d{2})-(?P<middle>\d{3,4})-(?P<rear>\d{4})', '02-123-1234')

print(result.groupdict())
print(result.groups())
print(result.group(1))
print(result.group('front'))


# {'front': '02', 'middle': '123', 'rear': '1234'}
# ('02', '123', '1234')
# 02
# 02
```



출처 - https://brownbears.tistory.com/506
