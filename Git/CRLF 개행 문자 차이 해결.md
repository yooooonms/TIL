# CRLF 개행 문자 차이 해결  

운영체제 별로 개행문자를 다르게 인식한다.  

`Window`에서는 개행 문자로 `CR(Carrige-Refurn, \r)`과 `LF(Line Feed, \n)`를 사용하지만  

`Linux` 또는 `Mac OS`는 `LF(Line Feed, \n)`만 사용한다.  

같은 프로젝트를 진행 중인데 서로 사용하는 운영체제가 다르거나 본인이 두 환경을 번갈아서 사용하는 경우에는 문제가 발생할 수 있다.  

예를 들어 코드의 변경점은 없는데 운영체제간 개행 문자의 차이로 인해 변경으로 착각해 파일이 `Modified` 상태로 변하는 문제가 생긴다.  

이런 문제를 해결하기 위해서는 운영체제가 달라도 문제가 발생하지 않도록 `CRLF` 설정을 해주어야 한다.  

## 해결    

- **core.autocrlf = false:** 기본 설정으로 `CRLF`를 사용하든 `LF`를 사용하든 `git`은 신경쓰지 않는다.
- **core.autocrlf = true:** `CRLF`를 `LF`로 변경한다.
- **core.autocrlf = input:** `LF`를 사용한다.

```git
# Windows

$ git config --global core.autocrlf = true
```

`Windows`에서는 `CRLF`를 사용하기 때문에 저장소에서 가져올 때 `LF`를 `CRLF`로 변경하고, `CRLF`를 `LF`로 변경할 수 있도록 `true`로 설정한다.  

```git
# Linux or Mac OS

$ git config --global core.autocrlf = input
```

`Linux` 또는 `Mac OS`는 `LF(Line Feed, \n)`만 사용하기 때문에 `input`으로 설정해 준다.  


#### 📌 Reference  

- <https://www.lesstif.com/gitbook/git-crlf-20776404.html>