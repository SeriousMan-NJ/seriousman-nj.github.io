---
layout: post
categories: ubuntu
tags: ubuntu ubuntu-16.04
comments: true
---
운영체제: Ubuntu 16.04 LTS

CentOS에서는 `useradd` 명령어를 통해 사용자를 추가할 수 있다. 우분투에서 useradd를 사용하면 사용자를 추가해주지만 홈디렉터리를 생성하지 않는다. 대신 우분투는 `adduser` 명령어를 통해서 사용자 ID, PW를 설정할 수 있고, 홈디렉터리도 만들어준다.

```bash
adduser USER # 사용자 ID, 비밀번호 등 설정
```

CentOS에서는 sudoers에 사용자를 추가하기 위해 사용자를 wheel 그룹에 추가했다. 우분투에서는 sudo 그룹에 추가해주면 된다.

```bash
usermod -a -G sudo USER
```

우분투에서 추가된 사용자는 자신의 홈디렉터리에 대한 권한이 없다(...) 그래서 다음과 같이 권한도 설정해주어야 한다.

```bash
$ sudo chown USER:USER /home/USER/
$ sudo chmod 750 /home/USER/ # 755를 줄 수도 있다.
```

# 참고문헌

홈디렉터리 권한 설정: [https://askubuntu.com/questions/747039/permission-denial-for-my-own-folder-myname-inside-home-folder](https://askubuntu.com/questions/747039/permission-denial-for-my-own-folder-myname-inside-home-folder)
