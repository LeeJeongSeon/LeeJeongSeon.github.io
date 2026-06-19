# Myvita 이메일 인증 안내 페이지

Supabase 이메일 인증(회원가입 확인·비밀번호 재설정) 링크를 PC 브라우저에서 클릭했을 때
빈 화면(딥링크 실패)이 뜨던 문제를 해결하는 정적 안내 페이지입니다.

## 동작
- 인증 자체는 Supabase 서버에서 이미 완료됨. 이 페이지는 **사용자 안내 + 앱으로 핸드오프**만 담당.
- 모바일: `healthapp://` 딥링크로 앱 열기 시도(인증 토큰을 그대로 전달) → 실패 시 "인증 완료" 안내.
- PC: 바로 "이메일 인증이 완료되었습니다. 앱으로 돌아가 로그인하세요" 안내.

## 배포 — 퍼블릭 페이지 허브 방식 (권장)
모든 프로젝트의 정적 페이지를 **user-site 레포 하나**(`<github아이디>.github.io`)로 모아 관리한다.
프로젝트마다 레포를 새로 파지 않고 **서브폴더**로 추가 → Pages 설정은 평생 1번.

Windows PowerShell:
```powershell
# verify-page 폴더에서 (gh 로그인 상태)
.\deploy.ps1                 # -> https://<아이디>.github.io/myvita-verify/
```
- 허브 레포(`<아이디>.github.io`)가 없으면 스크립트가 자동 생성.
- 다른 프로젝트 페이지도 같은 스크립트 패턴으로: `.\deploy.ps1 -Subpath 프로젝트이름`

> macOS/Linux는 `deploy.sh`(단일 레포 버전) 참고. 허브 방식은 위 PowerShell 스크립트가 표준.

## Supabase 연동
배포 URL이 나오면 Supabase 대시보드에서 교체:
- **Authentication → URL Configuration → Redirect URLs** 에 위 Pages URL 추가
- 이메일 템플릿(Confirm signup 등)의 `{{ .ConfirmationURL }}` 가 이 URL로 redirect 되도록
  `redirect_to` 가 Pages URL을 가리키게 설정

> 딥링크 스킴은 프로덕션(`healthapp`) 기준. dev client(`healthapp-dev`)는 별도 처리 불필요
> (인증 메일은 프로덕션 환경에서 발송).
