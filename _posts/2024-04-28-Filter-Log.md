---
title: [Filter를 통한 로그 설정]
author: excelsiorKim
date: 2024-04-28 22:47:03 +0900
categories: [Spring, Programming]
tags: [Spring, gradle, error]
keywords: [Spring, gradle, error]
description: 프로젝트 진행 과정에서 마주한 버그들 기록
toc: true
toc_sticky: true
---

# Filter를 통한 로그 설정하는 방법

![filter-img](/assets/img/2024-04-28-Fiter-Log/Filter.png)

- Filter 인터페이스를 구현한 커스텈 필터 클래스를 생성한다.

```java
@Component
public class LoggingFilter implements Filter {
    private static final Logger logger = LoggerFactory.getLogger(LoggingFilter.class);

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        // 로그 수집 로직 구현
        // ...
        chain.doFilter(request, response);
    }
}
```

- 위와 같이 구현 클래스를 하나 정의한 뒤 아래와 같이 `doFilter`메서드를 오버라이드하면 된다.

```java
@Override
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
        throws IOException, ServletException {
    long startTime = System.currentTimeMillis();

    HttpServletRequest httpRequest = (HttpServletRequest) request;
    String requestURI = httpRequest.getRequestURI();
    String method = httpRequest.getMethod();

    // 요청 정보 로그 기록
    logger.info("Request: {} {}", method, requestURI);

    chain.doFilter(request, response);

    long endTime = System.currentTimeMillis();
    long timeTaken = endTime - startTime;

    HttpServletResponse httpResponse = (HttpServletResponse) response;
    int status = httpResponse.getStatus();

    // 응답 정보 및 처리 시간 로그 기록
    logger.info("Response: {} {} (Time taken: {} ms)", method, requestURI, status, timeTaken);
}
```

- 하지만 위와 같은 방법으로 요청본문(request body)를 읽으려 했을 경우 `IllegalStateException`이 발생할 것이다.
- 왜냐하면 `HttpServlet`의 `getInputStream()`또는 `getReader()`를 사용하여 요청 본문(request body)를 읽으면. 스트림이 소비되고 더 이상 읽을 수 없게 된다.
  - 즉, 요청 본문을 한 번만 읽을 수 있도록 설계되어 있다. 이는 요청 본문이 스트림 형태로 제공되기 때문이다. 스트림을 한 번 읽으면 다시 읽을 수 없고, 재사용할 수 없다.

### 해결 방법

- 이를 해결하기 위해 요청 본문을 읽은 후 다시 재사용할 수 있도록 래핑(wrapping)하는 방법이다.

```java
@Override
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
        throws IOException, ServletException {
    ContentCachingRequestWrapper cachedRequest = new ContentCachingRequestWrapper((HttpServletRequest) request);

    // 필터 로직 수행
    // ...

    // 요청 본문 로그 수집
    String requestBody = getRequestBody(cachedRequest);
    logger.info("Request Body: {}", requestBody);

    // 다음 필터 또는 서블릿으로 요청 전달
    chain.doFilter(cachedRequest, response);

    // 추가적인 로그 수집 또는 처리
    // ...
}

private String getRequestBody(ContentCachingRequestWrapper request) throws IOException {
    byte[] contentAsByteArray = request.getContentAsByteArray();
    if (contentAsByteArray.length > 0) {
        return new String(contentAsByteArray, request.getCharacterEncoding());
    }
    return "";
}
```

- 위와 같이 `ContentCachingRequestWrapper`클래스를 사용하여 요청 본문을 읽은 뒤에도 캐시된 요청 본문이 유지되도록 할 수 있다.

- 그 외에는 요청 본문이 필요한 경우에만 로그를 수집하거나, 비동기 처리를 사용하여 이러한 문제를 해결 할 수 있다.
  - 비동기 서블릿이나 컨트롤러를 사용한 비동기 처리 시에는 요청 본문을 여러 번 읽을 수 있다.
