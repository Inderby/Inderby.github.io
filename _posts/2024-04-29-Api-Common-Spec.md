---
title: [Api 공통 스펙 정의 하기]
author: excelsiorKim
date: 2024-04-29 22:47:03 +0900
categories: [Spring, Programming]
tags: [Spring, gradle, api, common-spec]
keywords: [Spring, gradle, common-spec]
description: Api의 공통된 구조를 정의한다
toc: true
toc_sticky: true
---

# API의 공통된 스펙을 정의해야 하는 이유

- 일관성 유지 : API의 설계와 구현에 있어 일관성을 유지해줄 수 있다. 이는 개발자들이 API를 이해하고 사용하는 데 도움이 된다.
- 호환성 확보 : 서로 다른 시스템이나 애플리케이션 간에 원환할 통신과 데이터 교환을 위해서는 API의 호환성이 중요하다. 이를 공통된 스펙을 따름으로써 호환성을 확보할 수 있다.
- 개발 효율성 향상 : 공통된 스펙이 정의되어 있으면 개발자들은 핻장 스펙을 참조하여 API를 개발할 수 있다. 이는 개발 시간을 단축시키고 효율성을 높일 수 있다.
- 문서화 및 의사소통 용이
- 유지보수 및 확장성
- 에러 처리의 일관성
- 상호운용성 증대

## 예시

```json
{
  "result" : {
    "result_code" : 200,
    "result_message" : "OK",
    "result_description" : "성공"
  },

  "body" : {
    ...
    ...
  }
}
```

- 위와 같이 요청에 대한 응답 결과 중에 실질적인 데이터를 제외한 부가적인 정보들을 result 부분에 넣어줌으로써 통일된 스펙을 맞춰줄 수 있다.

### 실제 코드

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Api<T> {

    private Result result;
    @Valid
    private T body;

    public static <T> Api<T> OK(T data){
        var api = new Api<T>();
        api.result = Result.OK();
        api.body = data;
        return api;
    }

    public static Api<Object> ERROR(Result result){
        var api = new Api<Object>();
        api.result = result;
        return api;
    }

    public static Api<Object> ERROR(Error error){
        var api = new Api<Object>();
        api.result = Result.ERROR(error);
        return api;
    }

    public static Api<Object> ERROR(Error error, Throwable tx){
        var api = new Api<Object>();
        api.result = Result.ERROR(error, tx);
        return api;
    }

    public static Api<Object> ERROR(Error error, String description){
        var api = new Api<Object>();
        api.result = Result.ERROR(error, description);
        return api;
    }
}
```

- 이처럼 api에 대한 통일된 규격을 제공해주는 `Api` 클래스에서 제네릭 타입으로 body data를 지정하고, 그 외에 필요한 데이터들은 `Result`에 넣어주는 것이 가능하다.

```java
package org.delivery.api.common.api;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.delivery.api.common.error.ErrorCodeIfs;
import org.delivery.api.common.error.ErrorCode;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Result {
    private Integer resultCode;
    private String resultMessage;
    private String resultDescription;

    public static Result OK() {
        return Result.builder()
                .resultCode(ErrorCode.OK.getErrorCode())
                .resultMessage(ErrorCode.OK.getDescription())
                .resultDescription("성공")
                .build();
    }

    public static Result ERROR(ErrorCodeIfs errorCodeIfs){
        return Result.builder()
                .resultCode(errorCodeIfs.getErrorCode())
                .resultMessage(errorCodeIfs.getDescription())
                .resultDescription("에러가 발생함")
                .build();
    }
    public static Result ERROR(ErrorCodeIfs errorCodeIfs, Throwable tx){
        return Result.builder()
                .resultCode(errorCodeIfs.getErrorCode())
                .resultMessage(errorCodeIfs.getDescription())
                .resultDescription(tx.getLocalizedMessage()) // TODO : 보안상 위협이 될 수 있는 코드이긴하나 우선 붙여둠
                .build();
    }

    public static Result ERROR(ErrorCodeIfs errorCodeIfs, String description){
        return Result.builder()
                .resultCode(errorCodeIfs.getErrorCode())
                .resultMessage(errorCodeIfs.getDescription())
                .resultDescription(description)
                .build();
    }
}

```

- 공통 스펙 중 하나인 Result 클래스 내부에서는 HttpStatus코드와 메시지에 대해 커스텀한 내용을 내려줄 수 있다.
