---
layout: post
title: certbot으로 간단 https 설정
---

HTTPS 인증을 위해서는 인증서 발급과 여러 절차들을 걸쳐야 한다. 그런데 certbot을 이용하면 매우 간단하게 HTTPS 설정을 진행할 수 있다.

이 글에서는 nginx 에서 사용하는 방법을 간단히 알아보겠다.

## 방법

1. certbot을 ppa에 등록해준다.

   ```bash
   sudo add-apt-repository ppa:certbot/certbot
   ```

2. apt-get update

   ```bash
   sudo apt-get update
   ```

3. certbot 설치

   사실 설치할 때 `python3-certbot-nginx` 가 있는것은 서버가 python인 것과 별로 상관이 없다. Go 서버, Javascript 서버를 구성한다고 해도 같은 방법으로 가능하다.

   ```bash
   sudo apt-get install certbot python3-certbot-nginx
   ```

4. HTTPS 설정  
   `-d` 옵션에서 자신이 nginx에 등록한 사이트 도메인을 입력해주면, nginx에서 해당 부분을 certbot이 찾아서 자동으로 HTTPS 등록을 해준다.

   여러개의 URL도 한 번에 처리할 수 있다.

   ```bash
   sudo certbot --nginx -d www.neulhan.com -d hy-maps.com
   ```

   위의 커맨드를 실행하면 아래와 같이 HTTP 를 HTTPS 로 자동 리다이렉트 설정할 것이냐는 문구가 나오는데, 따로 설정하지 말고 cerbot 한테 redirect 부탁하자. 직접 하는 것 보다 훨씬 간편하다.

   ![]({{ site.baseurl }}/images/2021/04/27/certbot2.png)

5. 성공
   아래와 같은 화면이 나오면 성공. 바로 HTTPS 연결을 사용할 수 있다. Redirect 설정을 했다면, HTTP로 연결해도 자동으로 HTTPS로 리다이렉트 된다.
   ![]({{ site.baseurl }}/images/2021/04/27/certbot3.png)

## 주의사항

- certbot 도 일단은 HTTPS 인증서를 무료로 인증해주는 Let's Encrypt 를 사용하기 때문에, 너무 많이 실패하면 안된다.
  너무 많이 실패해버리면, Let's Encrypt 가 그렇듯이 해당 도메인에 대해서 인증이 잠겨버린다.

  경험적으로 5회 이상 실패했을 때 잠겨서 난처했던 기억이 있다. 1주일 정도면 보통 풀리지만, 신중하게 시도하기를 추천한다.

- 기본적으로는 certbot이 자동으로 갱신을 해주지만, 오랜 기간 접속이 없거나, public으로 오픈되지 않은 url이라면 인증서가 만료될 수 있다. 그런 경우에는 그냥 다시 시도하면 된다.

  인증서가 이미 존재하는 경우에 아래와 같은 화면이 나올 수 있는데, 새로 발급받아서 사용해도되고, 갱신해서 사용할 수도 있다.  
  무엇을 선택해도 크게 상관없는 것 같지만, 나는 그냥 새로 발급받는다.
  ![]({{ site.baseurl }}/images/2021/04/27/certbot1.png)

## TMI

- 그렇게 등록된 사이트는 HTTPS가 아주 잘 연결된다. ([https://hy-maps.com](https://hy-maps.com)) 해당 사이트는 예전에 동아리에서 강의할 때 멘티분들과 동아리 해커톤에서 뚝딱 만들었던 사이트이다. 딱히 웹 어플리케이션을 무엇을 쓰는지는 상관없지만 굳이 이야기하자면 해당 사이트는 Django이다.

- 회사 내부에서 Beta 서버를 운영할 때 보안그룹을 회사 내부 IP 만 열어둬서 종종 HTTPS 인증서가 만료되고는 했었다. 그럴 때마다 보안그룹 잠깐 열어서 인증받고 다시 닫는 식으로 처리했었다. 뭔가 더 나은 방법이 있을 것 같기도?
