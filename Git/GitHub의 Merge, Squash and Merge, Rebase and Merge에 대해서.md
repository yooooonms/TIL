GitHub는 Merge, Squash and Merge, Rebase and Merge 세 종류의 Merge를 지원한다.  

각 Merge 방식에 따라 커밋 히스토리가 달라진다.  

## Merge  

<img src="/Git/image/merge.png" width="450" height="150">  

A, B, C를 참조하는 M 커밋을 생성한다. M은 부모로 Init, C를 가진다.  

커밋 M부터 뒤로 되돌아가면서 부모를 모두 찾아 브랜치를 구성한다.  

## Squash and Merge  

<img src="/Git/image/squashmerge.png" width="600" height="150">  

A, B, C 를 합쳐 새로운 커밋을 생성하고 머지 대상 브랜치에 추가한다. A, B, C는 부로를 Init 하나만 가진다.  

## Rebase and Merge  

<img src="/Git/image/rebasemerge.png" width="650" height="150">  

이전에 수행되었던 A, B, C의 관계를 그대로 유지하면서 머지 대상 브랜치에 추가한다.  

A는 머지 대상 브랜치의 이전 커밋인 E를 부모로 가지게 된다.  

---

#### 📌 Reference  

- <https://meetup.toast.com/posts/122>
- <https://evan-moon.github.io/2019/08/30/commit-history-merge-strategy/>