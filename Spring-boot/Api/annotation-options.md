# produces
# Content-Type
# consumes
- MIME 타입 제한하기
- MIME 타입이란 클라이언트에게 전송된 문서의 다양성을 알려주기 위한 메커니즘
https://developer.mozilla.org/ko/docs/Web/HTTP/Basics_of_HTTP/MIME_types

- Request Body에 담는 타입을 제한할 수 있다.

```java

@GetMapping("/test", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)


```


```java
@Test
public void testMethod(){
	
    mockMvc.perform(post("/hello")
    		.contentType(MediaType.APPLIACTION_JSON_UTF8))
            .andDo(print())
            .andExpect(status().isOK());
            

}

```

-  @RequestBody , @ModelAttribute, @RequestParam 등 어노테이션을 사용하게 되면 해당 어노테이션과 매칭되는 "메시지 컨버터" 가 AnnotationMethodHandlerAdapter에 의해 등록되게 됩니다.

- model객체 앞에 @RequestBody 어노테이샨을 붙이면 AnnotationMethodHandlerAdapter에 의해 MappingJacksonHttpMessageConverter가 등록되게 됩니다.

- @ModelAttribute를 사용하면 FormHttpMessageConverter가 등록되어
key = value 를 model로 converting 하게 됩니다.
앞에 아무런 어노테이션도 안붙이고 Person person 으로 선언하면 @ModelAttribute가 암묵적으로 사용
- FormHttpMessageConverter는 MediaType이 application/x-www-form-urlencoded로 정의된 폼 데이터를 주고 받을 때사용하게 됩니다.


# headers

- headers라는 속성은 사용자가 요청하는 header에 특정 header가 존재하도록 제한할 때 사용

```java
@PostMapping( value="/hello", headers = {HttpHeaders.FROM} )
@ResponseBody
public String hello(){
	return "hello";
 }

```


### https://2ham-s.tistory.com/292