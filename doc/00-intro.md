# Git 내부 구조를 알아보자 (0) - 프로젝트 소개와 예고

![](doc/img/data-model-1.png)

안녕하세요, 저는 서버 개발자이고, 이름은 김대현이라고 합니다.

앞으로 연재할 내용은, Git의 내부 구조를 파악해 보겠다는 호기심에서 출발해, 간단하게 클로저로 그 내부를 다루는 소스코드를 직접 작성하며 남기는 개발 노트입니다. 글과 함께, 같은 내용을 동영상 스크린캐스트 방식으로도 남기려 하니, 동영상 매체를 선호하시는 분들은 곧 추가로 녹화할 유튜브 영상을 보셔도 좋을 것 같습니다. (나중에 올려놓고 링크를 걸도록 할게요)

미리 말씀드리자면, 이 연재는 Git을 일반적으로 사용하는 방법에 관한 내용은 아닙니다. Git의 내부 구조를 아는 것이 Git을 더 잘 사용하는데 도움이 될 수는 있어도, 가장 쉽고 빠른 방법은 아닐 것입니다. 단순한 지적 호기심을 만족하려거나, Git을 바탕으로 그 위에 무언가를 만들어 내는 등의 아주 특수한 활용을 목표로 할 때 유용한 내용입니다. Git의 일반적인 활용법에 관련한 내용을 찾으시는 분들은, 다른 더 좋은 자료를 참고해 주세요.

다시 말해 이 연재는, 그런 분이 많지는 않겠습니다만, Git 내부 구조를 파헤치며 지적 유희를 즐기시려거나, Git 저장소를 활용한 모종의 서비스를 개발하시려는 분들께 유용할 것 같습니다. 아니면 그간 관심이 없다가도 "이참에 한번 살짝 알아나 볼까?"하는 생각이 조금이나마 일으셨다면, 잘 오셨습니다. 저부터 공부하며 남기는 자료이므로, 함께 공부하는 느낌이 전해질 수도 있겠습니다.

아, Git의 기본 개념과 사용법에 대해서는 알고 계신다고 가정하고 진행할게요. 아직 몇 편을 진행할지 모르지만, 대략 서너 편의 글과 동영상으로 전달하려 합니다.

또 여러 편의 글과 함께 프로젝트를 진행할 것 같으니, 시작에 앞서 억지로나마 합리화를 해보겠습니다. 왜 Git의 내부 구조를 파악하는 일을 벌이는 걸까요?

## 합리화? (감성적 이유)

수년 전 Git을 처음 접하고, 그 기술적인 아름다움에 매료됐습니다. 딱딱하고 건조해 보이는, 아니 실상 별로 눈에 보이는 것이 없는 소프트웨어도 이따금 아름다운 모습을 드러내는데, Git의 VCS에 대한 접근과 구현이 그러했습니다.

아름다운 대상은 자세히 관찰해서 더 알고 싶은 법, 그 안에 돌아가는 일들을 알고 싶습니다. 이번 프로젝트도 실용적으로는 큰 결과물 없는 내용일지도 모르겠지만, 전 클로저를 "학습 중"이니까, 클로저로 이거 저거 연습해보는 셈 칠 수 있겠습니다.

그러니까, "클로저로 Git의 내부를 어루만지는 코드를 작성해 봄으로써 그 아름다움을 몸소 느껴보자"는 말씀입니다.

## 합리화! (이성적 이유)

아, 그런 감성적인 이유는 됐고, 이 걸 알아서 당최 어디에 쓸 수 있을까요? Git의 저장소를 다룰 수 있게 되면, 우선 직관적으로는 개발자 개인이나 팀이 공용 저장소로 쓸 수 있는 서비스를 만들 수 있습니다. 이미 깃헙과 빗버킷이 있는 마당에 또 무슨 저장소 서비스를 만드냐는 문제가 남긴합니다만, 왠지 만들고 싶은 걸요? 흠.

그리고, 소스코드 말고도, 간단한 웹문서의 데이터베이스로 쓸 수도 있습니다. 텍스트 문서를 기반으로 하는 어떤 웹서비스에든 쓸 수 있지요. 뒷단에 문서를 저장하는데 Git 저장소를 쓴다면, 텍스트를 편집하며 이력을 관리하고 차이점을 확인하거나, 몇 명이서 공동 작업을 하기에도 좋을 것 같다. GitBook 같은 시스템이 Git 저장소를 데이터베이스 삼아 책을 쓰는 것 아니던가요?

음, 역시나 충분한 합리화가 이뤄지지 못한 것 같지만, 일단 더 파 보겠습니다. 알 수 없죠, 더 파보다 보면, 어쩌면 보물이 나올지도?

## 프로젝트 범위와 진행 방법

이 프로젝트를 진행하는데 참고한 자료와 사용하는 도구를 먼저 말씀드립니다.

### 1. 온라인 문서 학습

먼저, [Git 공식 사이트](https://git-scm.com)에 있는 온라인 책을 보며 진행합니다.

* ProGit 10장: <https://git-scm.com/book/en/v2/Git-Internals-Git-Objects>
* 한글 번역본: <https://git-scm.com/book/ko/v2/Git의-내부-Plumbing-명령과-Porcelain-명령>

Git에 관련한 자세한 설명이 나온 이 책의 10장에 Git 내부 구조에 대한 설명이 나옵니다. 전체적인 개념을 파악하고 이해하기에 좋은 자료입니다. 출판된 책을 선호한다면, 해당 내용을 인사이트가 출판한 "프로 Git 2판" 구매 추천드립니다. 저도 구매한 책을 보며 이 프로젝트를 진행하고 있어요. 10장에 언급된 내용이 이 프로젝트의 범위와 대략 일치합니다.

### 2. 소스코드 작성

* JGit 소스코드 분석으로 실제 구현 내용 파악
* 클로저로 프로토타입 구현

앞서 언급한 문서와 책에는 전반적인 내용이 아주 잘 정리돼 있지만, 실제 구현을 위한 디테일은 과감히 생략돼 있습니다. 아무래도, 너무 자세한 디테일은 이해하는 데 오히려 방해가 될 수도 있겠죠. 그래서 디테일이 필요한 부분은, 이미 잘 작성된 오픈소스 프로젝트를 참고해서 역으로 분석하는 방법으로 진행합니다. git의 소스를 분석해도 되고, libgit2 라이브러리를 분석해도 됩니다. 제 경우는 자바로 작성된 JGit을 많이 참고해서 진행하려 합니다.

클로저라는 생소한 언어로 진행합니다만, 큰 개념을 따라가며 이해하시는 데는 별 문제없을 거예요. 만약 본격적으로 따라 하면서 함께 진행하시려 한다면, 본인이 선호하는 프로그래밍 언어로 직접 해보시는 것도 좋을 것 같습니다.

### 3. 개발노트와 스크린캐스트 작성

읽고 이해하고만 지나가거나 샘플 프로젝트만 작성하는 것도 큰 일이지만, 이번엔 일을 좀 더 크게 벌려서 스크린캐스트도 만들려 합니다. 아무래도 이런 건 따라해 보는 게 제일 좋습니다만 클로저를 따라 하실 분은 많지 않고, 또 바쁜 요새 세상에 일일이 따라 하는 것도 꽤 번거로운 일이잖아요. 가만히 편하게 누워서 동영상 보시면서도, 직접 일일이 따라한 것 같은 느낌이 들도록 스크린캐스트를 찍어 올리겠습니다. 이를 위해 외장 마이크도 샀다능~!

정리하겠습니다. Git 내부 구조를 파악하는 프로젝트를 진행하고, 스크린캐스트로 직접 보이며 분석할 예정입니다. 구독 누르시고, 추천 누르시고, 응원 댓글 남겨주시면, 프로젝트 진행이 더 빨라집니다. 그럼, 다음 편에 만나요, 제발~