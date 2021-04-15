# docker_basic  

Docker : 어플리케이션을 올릴 수 있는 플랫폼  

도커를 사용하는 이유  

ansible 같이 굳이 도커를 안써도 자동화를 사용하여 Application을 구축할 수 있다면 사용할 이유가 없지않은가?

그럼 장점?  
기본적으로 사용되는 베이스 이미지가 있어서 운영체제를 없앨 수 있음  
Application을 바로 구축할 수 있으니 플랫폼에 대한 문제가 없어짐  

라이브러리 디펜덴시에 구애받지않음  
라이브러리 디펜덴시 환경을 상쇄시킨다  

Docker proxy  
Docker host received 되는 패킷을 인터셉트해서 도커 컨테이너로 전달  

Docker -i -t run debian /bin/bash 처럼 사용가능  
-I 상호작용 플래그 -> 실제 접속해서 대화형으로 사용할 수 있게  
-t 터미널 생성 -> 터미널이 실제로 나오도록  
-t 옵션이 없이 run 하게되면 ls를 입력하여도 결과값은 나오지만 터미널 처럼 표시되지 않음.  

도커는 메인 프로세스가 종료되면 컨테이너가 종료됨 ->  
VM처럼 항상 떠있지 않고 메인 프로세스가 꺼지면 컨테이너도 꺼짐  

이건 도커의 생명주기와 같음  
create -> started -> running -> stopped -> Deleted  
                     Paused  
이런 생명 주기를 가지고있는데, docker -i -t run debian echo "Hello World" 실행 시 Hello Worldf를 출력하고 컨테이너가 종료됨 -> 프로세스가 종료되는 순간 그 다음 컨테이너가 종료  


이런 설정 환경이나 상태를 저장하고 사용하고 싶다. 라고 한다면  

Docker commit  
어떤 환경이 설치된 것을 그대로 가져갈 수 있게 하기위해서 사용한다.  

Docker commit silly_spence playground/cowsayimage  

Playground -> image namespace 한마디로 이름이 겹치지 않도록 구성하기 위해 나눈다  
Sily -> 호스트네임  
cowayimage는 이미지 이름  
컨테이너 데이터를 이미지로 만듬  

Docker run playround/cowsayimage cowboy “and” // 이렇게하면 만들어둔 데비안에 cowsay가 설치된 환경을 바로 사용하여 "and"라는 값을 출력할 수 있게 된다.  


이런 구성을 계속 하기가 힘듦 그래서 하는게  
dockerFile  

맨 첫줄은 FROM  
FROM : Debian/lates -> 데비안의 마지막 버전  
Run : apt update && apt install cowboy fortune -y // 사용할 명령어나 실행할 값을 적어둠  
COPY entrypoint.sh / //COPY 복사할 파일 -> 컨테이너 이미지 안에서 이 파일이 위치할 곳    

ENTRYPOINT ["/entrypoint.sh"] // 쉘 스크립트를 실행하게 할 수 있음 , 쉘을 실행할 수 있는 환경이여야함 /bin/bash와 같은  
 
ENTRYPOINT = 패턴  
위 이미지에서 cowsay를 사용하는데 만약 인자가 없다면? -> 값을 지정하지 않는다면 ?  
패턴을 써서 스크립트를 만든다  
아래 스크립트의 내용은  
아무것도 입력을 안하면 furturne  
입력하면 $@가  나오도록 구성  
!/bin/bash  
If [ $@ -eq 0 ]; then  
	/usr/games/fortune | /usr/games/cowsay  
 else  
	/usr/games/cowsay “$@“  
fi  

이미지를 만들 땐 docker build  
-t TAG 레포하고 태그가 t 옵션  
Playground/cowayimage 이런식으로  
docker bulid -t playground/cowsayimage ./ // dockerfile에 구성된 값을 불러와서 playground cowsayimage로 image 생성  
빌드 생성 시 자동으로 FROM apt-get update && 명령어가 실행됨  

이렇게 만든 이미지를  
docker run playround/cowsayimage 로 사용가능  
