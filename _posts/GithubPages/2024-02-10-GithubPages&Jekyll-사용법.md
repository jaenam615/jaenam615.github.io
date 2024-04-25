---
title: "윈도우에서 GitHub Pages와 Jekyll로 블로그 만들기"
writer: Langerak
date: 2024-02-10 11:33:00 +0800
categories: [GitHub Pages, Jekyll]
tags: [GitHub Pages, Jekyll]
pin: false
math: true
mermaid: true
image:
  path: https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/e3a98d9d-de55-4a7d-b0c4-2c945e9fa107
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: Jekyll
---

> 본 글은 제 개인적인 공부를 위해 작성한 글입니다. 틀린 내용이 있다면 언제든지 피드백을 주시면 감사하겠습니다. 참고로만 활용해주시길 바랍니다.   

> 윈도우에서 Jekyll과 GitHub Pages를 이용하여 블로그를 만들어보고, 그 내용을 정리한 글입니다.  
> Github, CMD는 이미 알고 있다고 가정하고 진행합니다.

## 1. Ruby 설치

[https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/)   
위 링크에서 WITH DEVKIT이 적혀있는 파일 중에서, 자신의 윈도우 운영체제에 맞춰 32bit or 64bit를 선택하여 최신 버전을 설치합니다.  
`x86 = 32bit, x64 = 64bit`  
윈도우에서 파일을 받으니 안전하지 않다 뜨는데, 그냥 진행합시다.

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/8bf295c6-7caa-496f-9d1c-93f9254632a5){: width="500" height="500" .center} 
_위 체크박스가 보이면 모두 체크 후 진행_

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/353c9fbb-d21b-4cb9-b0d5-fce66be95be6){: width="500" height="500" .center}
_설치 완료후 위 화면에서 1 + Enter_

![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/9b3808a0-511b-482d-95dd-7af942de8eb5){: width="500" height="500" .center}
_설치 완료 시 보이는 창_

위 사진까지 왔다면 이제 커맨드 창을 열어 `ruby -v`를 입력하여 루비 버전이 나와야 설치가 완료된 것입니다.

## 2. Jekyll & Bundle 설치

루비를 설치하면서 딸려온 `Start Command Prompt with Ruby`를 실행하여, 아래 커맨드를 입력하여 `jekyll`와 `bundler`를 설치합니다.
```shell
gem install jekyll bundler
```
마찬가지로 커맨드 창에 `jekyll -v`를 입력하여 버전이 나와야 설치가 완료된 것입니다.

## 3. Github Repository 생성

`{github-username}.github.io`를 이름으로 새 레포지토리를 생성합니다.  
public으로 생성하고, readme 파일은 생성하지 않습니다.  
그리고 자신이 원하는 로컬 폴더 위치에 클론합니다.

```shell
git clone https://github.com/{github-username}/{github-username}.github.io.git
```

## 4. Jekyll 사이트 생성

빈 레포지토리를 클론해왔으니, 해당 폴더로 이동하여 아래 커맨드를 입력합니다.   
만약 폴더 안에 파일이 있다면 모두 삭제하고 진행하셔야 합니다.

```shell
jekyll new ./
```

다음으로 `bundle`을 설치하고 업데이트합니다.

```shell
bundle install
bundle update
bundle install
```

## 5. Jekyll 로컬 테스트

아래 커맨드를 입력하여 로컬에서 사이트를 테스트합니다.

```shell
bundle exec jekyll serve
```

`http://127.0.0.1:4000/` 로 접속하여 사이트가 정상적으로 뜨는지 확인합니다.
![image](https://github.com/JeongJongMun/JeongJongMun.github.io/assets/101979073/d8106137-073a-4169-b2cd-a392da752f94){: width="500" height="500" .center}
_위 사진과 같이 나온다면, 로컬에서 테스트 성공입니다._

## 6. Jekyll 테마 다운로드

http://jekyllthemes.org/themes/jekyll-theme-chirpy/  
저는 위 링크에서 Chirpy 테마를 다운로드 받았습니다.
다운로드 받은 파일을 압축해제하고, 해당 폴더를 클론한 레포지토리에 덮어씌웁니다.

## 7. Jekyll 테마 적용

리눅스에서는 커맨드로 쉽게 초기화가 되지만, 윈도우에서는 되지 않는다 합니다..  
그래서 직접 수정을 해야 합니다.

1. `Gemfile.lock` 파일 삭제
2. `docs` 폴더 삭제
3. `.github/workflows/pages-deploy.yml.hook` 파일을 제외한 **나머지 파일 삭제**
4. `.github/workflows/pages-deploy.yml.hook` 파일명을 `pages-deploy.yml`로 변경
5. `_posts` 폴더는 삭제해도 되는데, 저는 삭제 안하고 참고용으로 두고 있습니다.

그리고 다시 루트 디렉토리로 돌아가 `bundle install`을 입력하여 업데이트를 진행합니다.

## 8. 블로그 설정 변경

`_config.yml` 파일을 열어서, 일부 설정을 변경합니다.

1. `lang` : `ko-KR`로 변경
2. `timezone` : `Asia/Seoul`로 변경
3. `title` : 자신의 블로그 이름으로 변경
4. `tagline` : 서브 타이틀로 변경
5. `description` : 블로그 설명으로 변경
6. `url` : `https://{github-username}.github.io`로 변경
7. `github_username` : 자신의 깃허브 아이디로 변경
8. `twitter_username` : 자신의 트위터 아이디로 변경
9. `social_name` : 자신의 이름으로 변경
10. `social_email` : 자신의 이메일로 변경
11. `social_link` : 원하는 소셜 링크로 변경

이정도 수정했다면, 다시 `bundle exec jekyll serve`를 입력하여 로컬에서 테스트합니다.

## 9. Github에 푸시

로컬에서 테스트가 완료되었다면, 아래 커맨드를 입력해서 깃허브에 푸시합니다.  
이때 위치는 루트 디렉토리에서 해야합니다.

```bash
git add .
git commit -m "원하는 커밋 메시지"
git push
```

푸시 후 Github Action을 통해 배포가 정상적으로 되는지 기다리고, `https://{github-username}.github.io`로 접속하여 블로그가 정상적으로 뜨는지 확인합니다.

## 10. Trouble Shooting

저는 위 과정을 진행하면서 몇가지 문제가 발생했습니다.

1. `깃허브 레포지토리 -> Settings -> Pages`에서 `Build and deployment`에서 `source`를 `Github Actions`로 변경하기
2. Conflict: The following destination is shared by multiple files. The written file may end up with unexpected contents.
   - 위 오류는 내가 다운 받은 Jekyll 테마 파일과 기본 Jekyll 파일이 같이 있어서 발생합니다.
   - `bundle exec jekyll serve`를 입력하면, 오류가 발생하는 파일을 알려주니, **index.markdown** 과 같은 중복된 파일을 삭제해주면 됩니다.
3. Github Action (build-test site) 오류
   ```
   At _site/404.html:1:
     internal script reference /assets/js/dist/page.min.js does not exist
   ```
   - 이렇게 깃허브에 푸시 후, 배포 과정에서 오류가 발생했습니다.
   - 해당 오류는 `assets/js/dist/` 폴더에 js 파일이 없어서 발생한 오류입니다.
   - 아래 명령어를 입력하여 해결했습니다. (npm install이 안된다면 node.js 설치)
     ```shell
     npm install -g win-node-env
     NODE_ENV=production npx rollup -c --bundleConfigAsCjs
     ```

참고

- [https://github.com/cotes2020/jekyll-theme-chirpy/issues/1090](https://github.com/cotes2020/jekyll-theme-chirpy/issues/1090)
- [https://devpro.kr/posts/Github-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-(1)/](https://devpro.kr/posts/Github-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-(1)/)
- [https://feb-dain.github.io/how-to-make-my-github-blog/](https://feb-dain.github.io/how-to-make-my-github-blog/)