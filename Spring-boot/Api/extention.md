# 스프링부트 설정파일

## yml
- ymal
> YAML Ain't Markup Language
> 설정파일 포맷

- pofile에 따라 환경을 다르게 설정한다.
- --- 를 이용해 환경설정을 구분할 수 있다.

```java
server:
    port:80
---
spring:
    profiles: local
server:
    port: 8080
---
spring:
    profiles: dev
server:
    port:8081
---
spring:
    profiles: prod
server:
    port: 8082
```

- application-{profile}.yml 파일로 분리할 수 있다.
- dev pofile로 실행하면 application-dev.yml 이 우선 설정이되고,
 나머지는 application.yml 설정이 적용된다.

 - Spring > Configuration Metadata
 https://docs.spring.io/spring-boot/docs/current/reference/html/configuration-metadata.html


---
## @Value

 - YMAL의 특징 중에 SpEL 평가만 지원한다. (유연한 바인딩, 메타데이터 지원을 하지 않는다.)
 - SpEL(Spring Expression Language, 스프링 표현 언어) 평가
  - 런타임에 객체 참조에 대해 질의하고 조작하는 기능을 지원
  - 특히 메서드 호출 및 기본 문자열 템플릿 기능을 제공

  ---
 ```java
 property:
    test:
        name: property depth test

propertyTest: test

propertyTestArray: a,b,c

propertyTestList: a,b,c

 
 ```

 ```java

import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

import java.util.List;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class ValueTest {
    @Value("${property.test.name}") // depth가 존재하는 값은 .으로 구분해서 값을 매핑
    private String propertyTestName;

    @Value("${propertyTest}") // 단일 값을 매핑
    private String propertyTest;

    @Value("${noKey:default value}") // 키 값이 존재하지 않은 경우 default 값을 설정하여 매핑
    private String defaultValue;

    @Value("${propertyTestArray}") // 배열형으로 매핑
    private String[] propertyTestArray;

    @Value("#{'${propertyTestList}' .split(',')}") // ,을 기준으로 리스트형으로 매핑
    private List<String> propertyTestList;

    @DisplayName("property @Value 학습테스트")
    @Test
    void valueAnnotationTest() {
        assertAll(
                () -> assertThat(this.propertyTestName).isEqualTo("property depth test"),
                () -> assertThat(this.propertyTest).isEqualTo("test"),
                () -> assertThat(this.defaultValue).isEqualTo("default value"),
                () -> assertThat(this.propertyTestArray).hasSize(3),
                () -> assertThat(this.propertyTestList).hasSize(3)
        );
    }
}
 ```


## @ConfigurationProperties

- *.properties , *.yml 파일에 있는 property를 자바 클래스에 값을 가져와서(바인딩) 사용할 수 있게 해주는 어노테이션

YMAL의 특징 중에 유연한 바인딩, 메타데이터를 지원합니다. 그러나 SpEL을 지원하지 않습니다.
@Value와 다른 점은 또한 prefix를 사용하여 값을 바인딩 합니다. 즉, 접두사를 활용하여 원하는 객체를 바인딩 해주며, 원하는 형을 선택하여 더 객체 지향적으로 프로퍼티의 매핑이 가능합니다.
새로운 의존성을 추가해줘야 가능하다.
You can easily generate your own configuration metadata file from items annotated with @ConfigurationProperties by using the spring-boot-configuration-processor jar. The jar includes a Java annotation processor which is invoked as your project is compiled. To use the processor, include a dependency on spring-boot-configuration-processor.
This dependency ensures that the additional metadata is available when the annotation processor runs during compilation.
The processor picks up both classes and methods that are annotated with @ConfigurationProperties. The Javadoc for field values within configuration classes is used to populate the description attribute.

 annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
사용하려면 해당 클래스를 Bean으로 등록해줘야 한다.
You cannot use constructor binding with beans that are created by the regular Spring mechanisms (e.g. @Component beans, beans created via @Bean methods or beans loaded using @Import)

1.5.1. 예제

```java


fruit:
  fruits:
    - name: banana
      color: yellow
    - name: apple
      color: red
    - name: water melon
      color: green

```

```java
import java.util.List;
import java.util.Map;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties("fruit")
public class FruitProperty {
    private final List<Map<String, String>> fruits;

    public FruitProperty(List<Map<String, String>> fruits) {
        this.fruits = fruits;
    }

    public List<Map<String, String>> getFruits() {
        return fruits;
    }
}

```

```java
import static org.assertj.core.api.Assertions.*;
import static org.junit.jupiter.api.Assertions.*;

import java.util.List;
import java.util.Map;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class FruitPropertyTest {

    @Autowired
    private FruitProperty fruitProperty;

    @DisplayName("Property ConfigurationProperty Test")
    @Test
    void configurationPropertyTest() {
        List<Map<String, String>> fruits = fruitProperty.getFruits();

        assertAll(
                () -> assertThat(fruits).hasSize(3),
                () -> assertThat(fruits.get(0).get("name")).isEqualTo("banana"),
                () -> assertThat(fruits.get(0).get("color")).isEqualTo("yellow")
        );
    }
}

```
---
## 1.5.2. POJO 매핑하기
기본 컬렉션 뿐 아니라 POJO 타입도 매핑해준다.
여기서 주의할 점은 setter가 무조건 있어야 바인딩이 가능하다
no need to provide constructor with argument.Only setters and getters are fineI


```java
number:
  numbers:
    - name: one
      value: 1
    - name: two
      value: 2
    - name: three
      value: 3

```

```java
public class Number {
    private String name;
    private int value;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getValue() {
        return value;
    }

    public void setValue(int value) {
        this.value = value;
    }
}

```

```java
import java.util.List;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties("number")
public class Numbers {
    private final List<Number> numbers;

    public Numbers(List<Number> numbers) {
        this.numbers = numbers;
    }

    public List<Number> getNumbers() {
        return numbers;
    }
}

```

```java
import static org.assertj.core.api.Assertions.*;

import java.util.List;

import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class NumbersTest {

    @Autowired
    private Numbers numbers;

    @DisplayName("POJO ConfigurationProperties Test")
    @Test
    void pojoConfigurationPropertiesTest() {
        final List<Number> numberList = this.numbers.getNumbers();

        assertThat(numberList).hasSize(3);
        assertThat(numberList.get(0).getName()).isEqualTo("one");
        assertThat(numberList.get(0).getValue()).isEqualTo(1);
    }
}

```

## 결론
![image](https://github.com/eldk2021/newday/assets/119994839/e4263abf-b495-44e7-a67c-49d11a0a862e)


위의 차이점을 알고, 필요할 때 더 정확한 annotation을 사용하는 것이 중요하다.
key값의 집합은 POJO로 바인딩을 해야 더 객체지향적이며, 빈 주입 구조화와 안전한 객체를 만들수 있습니다. 따라서 @ConfigurationProperties를 사용하는 것이 더 좋습니다.

### https://rutgo-letsgo.tistory.com/93
### https://programmer93.tistory.com/47

---
---

## @ConfigurationProperties

### 1. properties에서 오토컴플릿을 지원하도록 하기 위한 dependency를 추가

```java
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-configuration-processor</artifactId>
	<optional>true</optional>
</dependency>
 ```

 

### 2. 클래스 파일 생성

@ConfigurationProperties 이 좋은 이유 여러 표기법에 대해서 오토로 바인딩해 준다. ( 아래 참고 )

acme.my-project.person.first-name	properties 와 .yml에 권장되는 표기 방법 
acme.myProject.person.firstName	표준 카멜 케이스 문법.
acme.my_project.person.first_name	.properties와 .yml 에서 사용가능한 방법 ( - 표기법이 더 표준 )
ACME_MYPROJECT_PERSON_FIRSTNAME	시스템 환경 변수를 사용할 때 권장
 

@Component로 bean을 등록해야 한다.

@ConfigurationProperties에 prifix를 설정한다.

properties 파일에 있는 site-url.* 에 대하여 바인딩한다.

```java

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import lombok.Data;

@Component
@ConfigurationProperties(prefix = "site-url")
@Data
public class siteUrlProperties {
	private String naver;
	private String google;
	
}
 
```
### 3. 확인

```java
@Controller
@RequestMapping("/")
@Slf4j
public class MainController {
	
	@Autowired
	siteUrlProperties siteUrlProperties;
	
	@GetMapping("")
	@ResponseBody
	public String test(Model model) {
		return siteUrlProperties.getNaver();
	}
	
}
```

 

@ConfiguConfigurationProperties 어노테이션을 사용하여 property 값을 사용하면 매핑을 유연하게 할 수 있다는 장점이 있지만 SpEL를 사용할 수 없다.

SpEL를 사용할 때에는 @Value를 사용해야 한다.

 

그 외에는 @ConfiguConfigurationProperties를 사용하는게 코드가 깔끔해진다.