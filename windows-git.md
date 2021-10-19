# Windows에서 git 계정 관리

윈도우즈를 다시 설치하거나 어떤 이유에서는 push가 안될 때는 다음과 같이한다.&#x20;

1. 먼저 git push 명령어를 사용해서 git에 로그인 해야 한다.&#x20;
2. git config 명령어를 사용하여 사용자 이름과 이메일을 설정해야 한다.&#x20;

VSCode의 터미널 창에서 다음을 실행한다.&#x20;

```
git push <저장소명> <브랜치명> 
```

Windows 자격증명 관리에서 자격증명이 등록되어 있는지 확인한다.  자격증명 프로그램은 윈도우즈 검색 매뉴를 사용하여 검색한다.&#x20;



**제어판 - 사용자 계정 - 자격증명 관리자 **

![](<.gitbook/assets/image (42).png>)

**프로젝트의 이름 및 이메일 확인 **

```
//이름 확인
git config user.name
//이메일 확인
git config user.email 
```

**global 이름 및 이메일 확인 **

```
git config --global user.name 
git config --global user.email
```

**이름 삭제 **

Windows 환경에서 git 사용자 계정을 변경하기 위해서는 위 스크린샷과 같이 제어판에서 편집을 해야합니다.

```
// 이름 삭제 
git config --global --unset user.name 
// 이메일 삭제 
git config --global --unset user.email 

```



