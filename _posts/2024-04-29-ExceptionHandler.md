---
title: [ExceptionHandler를 통한 예외처리]
author: excelsiorKim
date: 2024-04-29 15:47:03 +0900
categories: [Spring, Programming]
tags: [Spring, gradle, exception, exceptionhandler]
keywords: [Spring, gradle, exception, exceptionhandler]
description: ExceptionHandler의 원리와 처리 방법에 대해 알아본다.
toc: true
toc_sticky: true
---

### Spring의 ExceptionHandler란?

- SpringMVC에서 예외 처리를 간편하게 할 수 있도록 도와주는 애노테이션이다.
- 동작 원리
  1. 예외 발생:
     - 컨트롤러 메서드 실행 중에 예외가 발생한다.
  2. DispatcherServlet에서 예외 감지:
     - DispatcherServlet은 컨트롤러 메서드 실행 중 발생한 예외를 감지한다.
  3. HandlerExceptionResolver 탐색:
     - DispatcherServlet은 예외를 처리할 수 있는 적절한 HandlerExceptionResolver를 찾는다.
     - 기본적으로 ExceptionHandlerExceptionResolver가 사용된다.
  4. @ExceptionHandler 애노테이션 탐색:
     - ExceptionHandlerExceptionResolver는 해당 컨트롤러 내에서 @ExceptionHandler 애노테이션이 붙은 메서드를 찾는다.
     - @ExceptionHandler 애노테이션에는 처리할 예외 클래스를 지정할 수 있습니다.
  5. 예외 처리 메서드 실행:
     - ExceptionHandlerExceptionResolver는 발생한 예외와 일치하는 @ExceptionHandler 메서드를 찾아 실행한다.
     - 예외 처리 메서드는 예외 객체를 인자로 받아 처리할 수 있다.
  6. 예외 처리 결과 반환:
     - 예외 처리 메서드는 예외 처리 결과를 반환합니다.
     - 반환 값은 뷰 이름, ModelAndView, ResponseEntity 등 다양한 형태로 지정할 수 있다.
  7. 예외 처리 완료:
     - DispatcherServlet은 예외 처리 메서드의 반환 값을 받아 적절한 응답을 생성하고 클라이언트에게 전송한다.

### 예시코드

```java
@Controller
public class MyController {

    // ...

    @ExceptionHandler(CustomException.class)
    public ResponseEntity<String> handleCustomException(CustomException ex) {
        // 예외 처리 로직
        String errorMessage = ex.getMessage();
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorMessage);
    }
}
```

- 위와 같이 컨트롤러 내부에서 `@ExceptionHandler`를 정의하는 것도 가능하지만, 아래와 같이 `@RestControllerAdvice`를 통해 여러 컨트롤러에서 발생하는 예외를 global하게 처리해줄 수 있다.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(CustomException.class)
    public ResponseEntity<ErrorResponse> handleCustomException(CustomException ex) {
        ErrorResponse errorResponse = new ErrorResponse(ex.getMessage());
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorResponse);
    }

    // 다른 예외 처리 메서드...
}
```

- 이를 통해 예외 처리 로직을 한 곳에서 관리할 수 있어 코드의 중복을 줄이고 유지보수성을 높일 수 있다.
- 또한, 예외 처리 결과를 일관된 형식으로 반환할 수 있어 API 클라이언트에게 더 나은 경험을 제공할 수 있다.
