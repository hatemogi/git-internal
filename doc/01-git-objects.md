# Git 내부 구조를 알아보자 : 1부 - 전체 구조 개요

안녕하세요, 김대현입니다. Git 내부 구조를 알아보자는 내용으로 글과 스크린캐스트를 연재하고 있습니다. 이 프로젝트에 대핸 소개나 왜 이걸 하는지, 이게 무엇에 도움이 되는지는 이전 글이나 동영상을 참고해 주세요.

* [미디엄 글: Git 내부 구조를 알아보자 (0) 프로젝트 소개와 예고](https://medium.com/happyprogrammer-in-jeju/git-내부-구조를-알아보자-0-프로젝트-소개와-예고-bf3a8549f439)
* [유튜브 동영상: Git 내부 구조를 알아보자 (0) 예고](https://youtu.be/DWnrsbxhuOY)

1부, 이번 편에서는 Git 내부에 저장되는 오브젝트들의 종류를 전반적으로 살펴보고 어떻게 쓰이는지 간략히 말씀 드리겠습니다. 자세한 내용과 소스코드는 2부에 이어질 예정입니다.

## Git 저장소 디렉토리의 내용

아시다시피 Git은 분산 버전 관리 시스템이고, 각각의 저장소가 독립적으로 완전한 데이터를 갖고 있을 수 있습니다. 원격 저장소 없이 로컬 저장소만으로도 버전 관리를 할 수도 있지요. 직접 저장소를 하나 만들어 보이면서 설명해 보겠습니다. Git을 써보신 분들은 다 아시는 내용이므로 이 부분은 빠르게 설명하고 넘어가겠습니다.

``` bash
$ mkdir 저장소 && cd 저장소
$ ls -A
$ git init
$ ls -A
$ cd .git
$ ls -p1
HEAD
config
description
hooks/
info/
objects/
refs/
$ find .
```

이렇게 git 저장소를 만들면, 작업 디렉토리에 .git/ 디렉토리가 생기고, 이 안에 git 저장소의 모든 내역이 담기게 됩니다. 이 디렉토리의 내용을 바탕으로 git의 모든 작업을 할 수 있습니다.

## Git 커밋, 브랜치, 그리고 HEAD 리뷰

먼저, Git 사용하시면서 잘 알게된 내용이실 텐데, Git 내부를 파헤치는 관점으로 다시 한 번 훑어보겠습니다. Git의 커밋 각각의 순간 프로젝트의 스냅샷을 담을 수 있지요. 커밋할 때의 전체 디렉토리 내용이 다 들어있습니다. 그리고 브랜치는 그 커밋들 중 하나를 가르키고 있는 포인터입니다.

## Git 저장소가 하는 일


## Plumbing & Porcelain


## JGit 소개

JVM 기반의 현대적 리스프로 불리는 Clojure는 Java 라이브러리를 그대로 가져다 쓸 수 있기에, JGit이라는 훌륭하고 탄탄한 Git 제어용 라이브러리를 쓰면 관련한 모든 일이든 할 수 있습니다. 그런데, Git 내부에 대해 아직 익숙하지 않기 때문인지, API 문서를 봐도 잘 이해가 되지 않고, 어떤 메소드를 어떻게 불러야할지 잘 모르겠더군요.

오래전에 공식 Git 사이트에 있는 문서로 Git 내부 구조를 공부한 적이 있어, 어렴풋이 어떻게 저장되는지 감은 잡고 있지만, 아직 뿌연 안개 속에 있는 기분이다. 이참에 다시 문서를 파헤쳐보며 연습용 코드를 작성해 봐야겠다. 그리고 이왕하는거, JGit을 불러다 쓰는 것도 좋지만, 그냥 순수(!) 클로저로 작성해보면 재밌겠다는 욕심까지 든다.

Git은 content-addressable 파일시스템(파일 내용을 주소로 활용)이다. Git의 내부는 그저 단순한 키-값 저장소일 뿐이며, 사실상 어떤 종류의 데이터도 저장할 수 있다. Git에 데이터를 저장하면, 내용에 해당하는 키값을 돌려주며, 이 키를 써서 나중에 해당 데이터를 언제라도 찾아올 수 있다.
