---
layout: post
title: Design - Rest API 디자인 가이드
description: Design - Rest API 디자인 가이드
modified: 2016-08-29
tags: [design, rest]
comments: true
image:
  feature: abstract-11.png
---

REST API 설계시 참고할만한 
[Best Practices for Designing a Pragmatic RESTful API](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#requirements)라는 좋은 문서가 있어 그 내용을 간단히 정리해 본다.  
더 자세히 알고 싶다면 링크를 따라가 확인해보자. 

##### [API는 개발자를 위한 UI이다. 그러니까 잘 좀 만들어라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#requirements)

##### [REST스러운 URL과 명령을 사용해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#restful): 명사, 동사, 복수, 단수, CRUD에 맞지않는 명령들 처리 등을 REST스럽게 정의해라. 

##### [예외를 두지말고 SSL을 사용해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#ssl)

##### [별도 문서 없이 API 자체만으로 이해가기 쉽게 해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#docs)

##### [항상 API 버전을 구분하고, header가 아닌 URL을 통해 명시해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#versioning)

##### [결과의 정렬, 필터링, 검색 명령은 쿼리스트링을 이용해 정의해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#advanced-queries)

##### [API 반환시 필드 숫자를 제한하는 방법을 제공하라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#limiting-fields)

##### [POST, PATCH, PUT 요청에 대해 의미 있는 결과를 반환하라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#useful-post-responses)

##### [HATEOAS는 아직 사용할만큼 성숙하지 못하다](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#hateoas)

##### [가능하면 json만을 사용해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#json-responses)

##### [json에는 camelCase를 사용해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#snake-vs-camel)

##### [pretty print를 default로 제공하고, gzip 압축은 옵션으로 제공해라](Pretty print by default & ensure gzip is support)

##### [결과 데이터에 대해 envelope을 default로 하지말고 옵션으로 제공해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#envelope)

##### [POST, PUT, PATCH의 요청 시 json을 고려해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#json-requests)

##### [Link 헤더를 사용해 page를 표현해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#pagination)

##### [Resource 내의 추가 정보를 획득하기 위한 방법을 제공해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#autoloading)

##### [HTTP method를 override할 수 있는 방법을 제공하라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#method-override)

##### [너무 많은 요청을 거절하는 적절한 response header를 제공하라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#rate-limiting)

##### [OAuth2 지원 시 적절한 API가 구현되어야 한다](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#authentication)

##### [결과 반환시 Cache 지원을 위한 tag를 header에 포함하라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#caching)

##### [클라이언트가 이용할 만한 에러 payload를 정의하라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#errors)

##### [HTTP Status code를 효과적으로 사용해라](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api#http-status)


