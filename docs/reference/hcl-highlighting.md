---
layout: default
title: HCL 코드 하이라이트 이슈와 해결 방법
nav_order: 99
parent: Reference
---

## 개요
GitHub Pages(Just the Docs + Rouge) 환경에서 HCL 코드 블록의 연산자(`=`)와 구두점(`,`, `{`, `}`)이 붉게 표시되는 문제가 발생할 수 있습니다. 로컬 Jekyll 빌드에서는 정상인데, 원격( GitHub Pages )에서만 색이 다르게 보이는 케이스입니다.

## 증상
- HCL 코드 블록에서 `=`와 `,` 등이 빨간색으로 표시됨
- 같은 문서 내에서도 어떤 블록은 정상, 어떤 블록은 빨강으로 보이는 혼재 현상

## 원인
- GitHub Pages의 Rouge/테마 버전과 로컬 빌드 환경의 버전/설정 차이
- Rouge의 HCL 렉서가 토큰을 `.o`(operator), `.p`(punctuation)뿐 아니라 `.nt`(name/tag), `.vi`(variable), `.nx`(name) 등으로 분류하고, 테마 기본 규칙이 이 토큰들을 붉은색으로 칠함

## 해결 방법

### 1) 코드 펜스 언어를 `hcl` → `terraform`으로 변경 (권장)
Rouge의 Terraform 렉서를 사용하면 토큰 분류가 더 중립적이어서 테마와 충돌이 줄어듭니다.

변경 전:
```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
}
```

변경 후:
```terraform
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
}
```

### 2) CSS로 최종 오버라이드 (보조 수단)
환경/캐시/로드 순서에 따라 남는 경우, 다음 선택자에 본문색을 강제할 수 있습니다.

`_sass/custom/custom.scss` 또는 인라인( `_includes/head_custom.html` )에 추가:
```css
.language-hcl .o,
.language-hcl .p,
.language-terraform .o,
.language-terraform .p,
.highlighter-rouge .language-hcl .o,
.highlighter-rouge .language-hcl .p,
.language-hcl .highlight .o,
.language-hcl .highlight .p,
.language-hcl .nt,
.language-hcl .vi,
.language-hcl .nx,
.highlighter-rouge .language-hcl .nt,
.highlighter-rouge .language-hcl .vi,
.highlighter-rouge .language-hcl .nx {
  color: var(--body-text-color, #383942) !important;
}
```

색 체계를 스킴에 맞추려면 `blue` 스킴 파일(예: `_sass/custom/blue.scss`)에도 같은 규칙을 추가하세요.

## 검증 방법
1. 로컬: `bundle exec jekyll serve`로 확인
2. 원격: 배포 후 1~2분 뒤 강력 새로고침(Cmd/Ctrl+Shift+R) 또는 쿼리 파라미터로 캐시 우회:
   - `...?v=2` 등

## 결론
로컬/원격 Rouge 차이로 발생하는 스타일 불일치를 가장 안정적으로 해결하는 방법은 코드 블록 언어를 `terraform`으로 지정하는 것입니다. 필요 시 CSS 오버라이드로 보조 조치하세요.


