## Post-Redirect-Get(PRG) 

## PRG 패턴이란?
PRG(Post/Redirect/Get) 패턴은 중복 양식 제출을 방지하는 웹 개발 디자인 패턴이다.
이름 그대로 HTTP Post 요청에 대한 응답이 또 다른 URL로의 Get 요청을 위한 Redirect 여야 한다는 것을 의미한다.

![image](https://github.com/ysj13/til/assets/59955248/7899d627-fff4-4ee9-9987-bebb48084bb0)
  https://media.vlpt.us/images/maketheworldwise/post/77c362d7-6351-4c17-8551-c06bc2e19341/image.png

## PRG 패턴을 사용해야 하는 이유
1. 웹 양식(form) 을 HTTP Post 요청을 통해 서버에 제출한 후
	브라우저를 새로고침하면 이전에 제출된 응답이 다시 제출되는 현상을 방지하기 위함이다.  
  ex) 사용자가 상품을 결제한 후 실수로 새로고침을 눌렀을 때 같은 요청이 반복되어
   결제가 두번  일어나게 되는 치명적인 오류 발생

3. HTTP Post 요청에 대한 결과 페이지를 북마크 하거나 주소를 공유하는것이 어려워진다.

## RedirectAttributes
리다이렉트가 발생하면 원래 요청은 끊어지고, 새로운 HTTP Get 요청이 발생하기 때문에 리다이렉트 실행 이전에 수행된 모델 데이터는 소멸한다.  
따라서 리다이렉트로 모델을 전달하는것은 의미없고, 스프링에서 제공하는 RedirectAttributes 를 이용하여 전달하면 된다.

```java
@PostMapping("/send")
public String send(RedirectAttributes redirectAttributes) {
  ...
  redirectAttributes.addAttribute("message", "전송완료");
  redirectAttributes.addFlashAttribute("message", "전송완료");
  
  return "redirect:/home";
}
```
위 코드와같이 addAttribute 또는 addFlashAttribute 를 사용해서 데이터를 담아주면 된다. 차이점은 아래와 같다.
> addAttribute는 유지되는 데이터를 전달하기 위해 사용되고,  
addFlashAttribute는 일회성 데이터를 리다이렉트 이후에 전달하기 위해 사용된다.


1. addAttribute:
   - addAttribute는 데이터를 유지하며 전달한다. 리다이렉트 이후에도 데이터가 유지되며, 다른 요청이나 뷰에서도 사용할 수 있다.
   - 데이터는 URL 매개변수로 전달되거나, POST 요청의 요청 본문에 포함되어 전달될 수 있다.
   - 주로 다음 요청이나 뷰에서 데이터를 사용해야 할 경우에 사용된다.

2. addFlashAttribute:
   - addFlashAttribute는 데이터를 일회성으로 저장한다. 즉, 리다이렉트 이후에는 데이터가 사라진다.
   - 일반적으로 리다이렉트 이후에 데이터를 한 번만 사용해야 할 때 사용된다.
       ex)리다이렉트된 페이지에서 한 번만 데이터를 읽어야 할 경우
   - 내부적으로는 HTTP 세션에 데이터를 저장하고, 리다이렉트된 페이지에서는 데이터를 읽은 후 세션에서 제거한다.
