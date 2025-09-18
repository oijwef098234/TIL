```bash
cd 커밋할 폴더
git init
git add .
git commit -m "Initial commit"
git remote add origin <https://github.com/너의계정명/레포이름.git>
git branch -M main
git push -u origin main
```

---

### 이미 origin이 만들어져있다면?

```bash
git remote remove origin
git remote add origin <https://github.com/계정명/레포이름.git> 
git push -u origin main 
```

### 올린 파일들이 git에서 추적되고 있는지 확인하기

```bash
git status
```

### 걍 아무것도 모르겠으면

```bash
rm -rf .git
```

이거 하고 다시 처음부터 ㄱ

---

```bash
 ! [rejected]        main -> main (fetch first)
error: 레퍼런스를 '<https://github.com/oijwef098234/PostCRUD.git'에> 푸시하는데 실패했습니다
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

이런 에러 → 걍 병합해라

밑에는 병합하는 코드

### 병합

```bash
git pull origin main --allow-unrelated-histories
git push -u origin main
```

### 최후의 수단 → 강제로 덮어쓰기

```bash
git push -f origin main
```
### 조직 레포로 푸쉬해야한다면 권한 필요

```bash
git remote set-url origin git@github.com:GuJeuk-Check-in/GuJeuk-Check-in_BE.git
git ls-remote origin
git push -u origin main
```