---
layout: post
comments: true
title: WEB API 동영상 스트리밍이 크롬에서 검색이 되지 않는 문제(.NET Framework)
date: 2020-12-12 10:18:00
categories: dev
---

### 원래 코드

```c#
// HomeController.cs
        public HttpResponseMessage Get()
        {
            string path = @"d:\Users\jason\Desktop\sample.mp4";
            FileStream file = File.OpenRead(path);

            HttpResponseMessage result = new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StreamContent(file)
            };
            result.Content.Headers.ContentType = new MediaTypeHeaderValue("video/mp4");
            result.Content.Headers.ContentRange = new ContentRangeHeaderValue(0, file.Length);
            return result;
        }
```

**sample.mp4**라는 파일을 읽어들여서 스트리밍을 하는 간단한 web API 코드이다.
해당 api에 접속을 하면 동영상이 재생을 되는 것을 확인할 수 있다... 만

### 문제점

그런데 구글 크롬에서는 동영상 seek바가 컨트롤이 안되고, 사파리에서는 아예 재생이 되지 않는 문제가 있었다.
![5](http://localhost/content/images/2020/12/5.png)
왼쪽이 구글 크롬으로 빨간 부분을 클릭해도 동영상 검색이 안되고, 사파리는 아예 재생 조차 불가능하였다.

### 해결

원인은 https://stackoverflow.com/questions/8088364/html5-video-will-not-loop 여기서 발견 할 수 있었다.
그러니까 Range헤더 요청에 대해서 Partial Content 응답을 하지 않아서 발생하는 문제라고 한다. 즉, 브라우저의 범위 요청을 수락하지 않으면 크롬에서 동영상 검색이 안된다는 것이다.

### 수정한 코드

Range헤더 요청이 있을때는 PartialContent응답을 해서 보내도록 수정을 하였다.
```c#
// HomeController.cs
        public HttpResponseMessage Get()
        {
            string path = @"d:\Users\jason\Desktop\sample.mp4";
            FileStream file = File.OpenRead(path);

            if (Request.Headers.Range != null)
            {
                HttpResponseMessage partialResponse = Request.CreateResponse(HttpStatusCode.PartialContent);
                partialResponse.Content = new ByteRangeStreamContent(file, Request.Headers.Range, "video/mp4");
                return partialResponse;
            }
            else
            {
                HttpResponseMessage result = new HttpResponseMessage(HttpStatusCode.OK)
                {
                    Content = new StreamContent(file)
                };
                result.Content.Headers.ContentType = new MediaTypeHeaderValue("video/mp4");
                result.Content.Headers.ContentRange = new ContentRangeHeaderValue(0, file.Length);
                return result;
            }
        }
```
이렇게 고치니 사파리, 크롬에서도 잘 작동하는 것을 확인하였다.
![1](http://localhost/content/images/2020/12/1.png)