# YongJun Academic Portfolio

GitHub Pages와 [AcademicPages](https://github.com/academicpages/academicpages.github.io)를 기반으로 만든 개인 학술 포트폴리오입니다. 연구 관심사, 교육 배경, 프로젝트, CV를 한곳에서 관리하도록 구성했습니다.

## Local Preview

### 준비물

- Ruby 3.2 이상
- Bundler

### 실행

```bash
bundle config set --local path vendor/bundle
bundle install
bundle exec jekyll serve -l -H localhost
```

브라우저에서 `http://localhost:4000`을 열면 됩니다. 배포용 결과만 확인하려면 다음 명령을 사용하세요.

```bash
bundle exec jekyll build --strict_front_matter
```

생성된 사이트는 `_site/`에 저장됩니다.

## Structure

```text
.
├── _config.yml                 # 이름, 소개, 프로필 링크, 사이트 주소
├── _data/navigation.yml        # About / Projects / CV 메뉴
├── _pages/
│   ├── about.md                # 홈(소개, 관심 분야, 학력, 대표 프로젝트, 기술)
│   ├── projects.md             # 전체 프로젝트 목록
│   ├── kookmin2026.md          # 국민대 자율주행 대회 상세 사례
│   └── cv.md                   # CV 안내 또는 PDF 링크
├── images/
│   ├── profile-placeholder.svg # 임시 프로필 이미지
│   └── projects/               # 프로젝트 이미지
├── files/                      # CV PDF 등 다운로드 파일
├── _sass/_portfolio.scss       # 포트폴리오 전용 스타일
└── .github/workflows/pages.yml # GitHub Pages 자동 빌드·배포
```

## Editing

### 1. 프로필 정보

`_config.yml`의 `title`, `description`, `author` 항목을 수정하세요. 현재 확인되지 않은 이메일은 비워 두었습니다.

프로필 사진은 예를 들어 `images/profile.jpg`에 추가한 뒤 `_config.yml`에서 다음처럼 바꿉니다.

```yaml
author:
  avatar: "profile.jpg"
```

### 2. 프로젝트 추가

1. 홈의 대표 프로젝트는 `_pages/about.md`에서 편집합니다.
2. 전체 목록은 `_pages/projects.md`에서 편집합니다.
3. 상세 사례가 필요하면 `_pages/<slug>.md` 파일을 만들고 아래 front matter를 사용합니다.

```yaml
---
title: "Project title"
permalink: /projects/<slug>/
author_profile: true
---
```

이미지는 `images/projects/<slug>/`에 넣고 Markdown 또는 HTML에서 `/images/projects/<slug>/파일명`으로 참조하세요.

### 3. CV 연결

CV PDF를 `files/YongJun_CV.pdf`로 추가한 뒤 `_pages/cv.md`의 안내 문구를 다음 링크로 교체합니다.

```liquid
[Download CV]({{ '/files/YongJun_CV.pdf' | relative_url }})
```

### 4. 국민대 프로젝트 내용 갱신

`_pages/kookmin2026.md`에서 결과, 기여 내용, 수치, 다이어그램을 관리합니다. 대회 기록과 구현 근거가 바뀌면 본문과 `images/projects/kookmin2026/`의 이미지를 함께 업데이트하세요.

## Deploy to GitHub Pages

1. GitHub에서 공개 저장소 `juuny0317-cmd.github.io`를 만듭니다.
2. 이 폴더의 파일을 저장소 `main` 브랜치에 push합니다.
3. 저장소의 **Settings → Pages → Build and deployment → Source**를 **GitHub Actions**로 선택합니다.
4. `pages.yml` 워크플로가 완료되면 `https://juuny0317-cmd.github.io`에서 사이트를 확인합니다.

```bash
git init -b main
git add .
git commit -m "Build academic portfolio"
git remote add origin https://github.com/juuny0317-cmd/juuny0317-cmd.github.io.git
git push -u origin main
```

사용자 사이트가 아닌 프로젝트 저장소에 배포할 경우 `_config.yml`의 `url`과 `baseurl`을 저장소 경로에 맞게 조정하세요.

## Main Routes

- `/` — About
- `/projects/` — Projects
- `/projects/kookmin2026/` — 국민대 자율주행 대회 상세 사례
- `/cv/` — CV

## License

기반 테마의 라이선스는 [LICENSE](LICENSE)를 확인하세요. 프로젝트 이미지와 개인 콘텐츠의 사용 권한은 각 원본 저장소 및 작성자에게 있습니다.
