# IntelliJ 사용법


## Git 
### 사용자 정보 설정

* commit/push 하기 전에 반드시 사용자 정보 확인하고 변경할 것 

global 이름 말고 프로젝트의 local 이름을 확인해야 한다. 


```shell
//이름 확인
git config user.name
//이메일 확인
git config user.email 
```

Local  이름 및 이메일 확인
```shell
git config --local user.name 
git config --local user.email
```

Local 이름 확인은 '--local' 옵션사용. 
또는 만약에 각 프로젝트마다 각각의 다른 정보를 사용하고 싶다면 --global옵션을 빼고 사용해주면 된다. 


**이름 변경**
```shell
 git config --local user.name 이름 
 git config --local user.email 이메일 
```



### Update  Project
Git 메뉴에서 Update Project를 선택하면 Merge할 것인지 Rebase할 것인지 물어 본다. 



### Fetch 
Git pannel의 왼쪽 사이드 아이콘 바에서 Fetch All Remotes 선택 
![](./.gitbook/assets/intellij/2021-12-01-12-30-44.png)


![](./.gitbook/assets/intellij/2021-12-01-12-14-42.png)




### 원격 브랜치에 브랜치 Push 

