# 코드 저장소 (fastmcp 4.0판)

책 본문(`원고/`)의 코드입니다. 본문의 코드는 읽으시라고 넣은 것이고, **실행하실 코드는 여기서 받으세요.**

이전 안정판(3.4.7) 동결본은 저장소에 두지 않습니다. 두 세대가 같은 코드 묶음을 쓰기 때문입니다. **이 코드는 `3.4.7`·`4.0.0b2`·`4.0.3` 셋에서 점검을 전부 통과합니다**(3.4.7 은 2026-09-10 재확인). 책이 4.0.3 을 기준으로 삼은 이유는 코드가 아니라 규격입니다 - 3.4.x 는 2026-07-28 개정 이전 세대입니다.

---

## 검증 상태

| 항목 | 값 |
|---|---|
| 검증일 | 2026-09-10 |
| fastmcp | **4.0.3** |
| mcp (기반 SDK) | 2.1.1 (2.2.0 에서도 통과. `fastmcp` 만 고정하면 설치 시점에 따라 갈립니다) |
| Python | 3.10 이상이면 됩니다 (검사는 3.13.3) |
| 검사 환경 | Windows 11 |
| 점검 결과 | **53개 항목 전부 통과** (다섯 단계 36 + 경계 17) |
| 이 판의 코드 | 이 저장소의 최신 커밋입니다. 출간 시점에 그 판을 태그로 고정하고 여기에 이름을 적습니다 |

**3.4.7 에 서버 CIMD 모듈이 없다고 적었던 것은 틀렸습니다.** 2026-09-10 에 공식 wheel 을 받아 확인했고, `fastmcp/server/auth/cimd.py` 의 `CIMDClientManager` 가 3.4.7 에도 있습니다. 그 판에서 5단계를 실제로 돌려 점검 53개가 통과하는 것도 확인했습니다.

**5단계는 클라이언트 등록을 두 방식으로 받습니다.** 클라이언트 정보 문서(CIMD)와 동적 등록입니다. 자기 문서가 없는 클라이언트가 있어서 옛 창구도 열어 둡니다. **CIMD 쪽 파이썬 API에는 아직 베타 표시가 붙어 있습니다.** 자세한 것은 책 부록 G에 있습니다.

---

## 설치

```
python -m venv .venv
source .venv/bin/activate        # 윈도우 PowerShell 은 .\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

`httpx`가 requirements에 함께 있습니다. 3.4.7과 달리 4.0은 `httpx`를 딸려 설치하지 않는데, `test_smoke.py`와 최종 장의 `todo_adapter.py`가 씁니다. 1~5단계 서버 코드 자체는 필요 없습니다.

## 파일

| 파일 | 단계 | 내용 |
|---|---|---|
| `todo_v1.py` | 1 | stdio. 사용자 개념 없음 |
| `todo_v2.py` | 2 | `user` 필드. 자기 신고 |
| `todo_v3.py` | 3 | HTTP + 헤더 토큰 |
| `todo_v4.py` | 4 | 파일 토큰 저장소 |
| `todo_v5.py` | 5 | OAuth + 로그인 화면 |
| `issue_token.py` | 4·5 | 토큰 발급·조회 |
| `check_v3.py` | 3·4 | Inspector 우회용 점검 (부록 A) |
| `todo_adapter.py` | 최종 장 | v5에서 도구 몸통만 서비스 API 호출로 바꾼 것 |
| `service_api.py` | 최종 장 | "여러분 서비스 API" 자리에 서는 가짜 API (:8001) |
| `test_smoke.py` | - | 다섯 단계 점검 + 경계 점검 (50항목) |
| `ERRATA.md` | - | 정오표. 책이 나간 뒤 발견된 잘못과 낡은 곳 |

## 실행

```
(.venv) $ python todo_v1.py                # stdio - Claude Desktop 이 실행합니다
(.venv) $ python todo_v3.py                # http://127.0.0.1:8000/mcp
(.venv) $ python check_v3.py               # 3단계 점검 (Inspector 대신). 인자로 토큰 하나, MCP_URL 로 배포 주소
(.venv) $ python issue_token.py kim        # 토큰 발급
(.venv) $ python todo_v4.py                # 0.0.0.0:8000
(.venv) $ python todo_v5.py                # OAuth
(.venv) $ python service_api.py            # 최종 장 - 가짜 서비스 API :8001 (먼저)
(.venv) $ python todo_adapter.py           # 최종 장 - 몸통 교체판 :8000
```

`(.venv) $` 는 가상환경이 켜진 터미널이라는 표시입니다. 입력하지 않습니다.

`todo_v5.py` 를 띄우기 전에 `BASE_URL` 을 실제 접속 주소로 맞춰 주세요. 로그인 화면 주소가 여기서 나옵니다.

```
(.venv) $ BASE_URL=https://내주소 python todo_v5.py
# 윈도우 PowerShell: $env:BASE_URL="https://내주소"; python todo_v5.py
```

## 점검

```
(.venv) $ python test_smoke.py
```

다섯 단계를 순서대로 돌려 봅니다. 5단계는 등록·인가·로그인·코드 교환·도구 호출까지 전 과정을 왕복합니다. 마지막에 **두 계정이 서로의 데이터를 못 본다**는 것까지 확인합니다.

## 미리 만들어 둔 계정 (5단계)

| 아이디 | 비밀번호 |
|---|---|
| kim | `kim-pw` |
| lee | `lee-pw` |
| park | `park-pw` |

실습용입니다. 실전에서는 이 자리에 여러분의 회원 데이터베이스가 섭니다.

---

## 실행하다 걸리기 쉬운 곳 (4.0 실측)

**데이터 파일 경로는 스크립트 기준입니다.** v1~v3의 `DB`는 `pathlib.Path(__file__).parent / "todos.json"`입니다. stdio 서버의 작업 디렉토리는 클라이언트(Claude Desktop)가 정하므로, 상대경로로 쓰면 Permission denied가 나거나 엉뚱한 폴더에 파일이 생깁니다 (2026-08-14 실측).

**`get_http_headers()` 는 4.0에서도 기본으로 `authorization` 을 지웁니다.** 그래서 3·4단계는 이렇게 씁니다.

```python
get_http_headers(include={"authorization"})
```

그냥 `get_http_headers()` 로 쓰면 토큰이 항상 빈 문자열이 되고 **모든 요청이 실패**합니다. 증상이 "토큰이 잘못됐다"로 보여서 엉뚱한 곳을 뒤지게 됩니다.

**클라이언트 등록은 4.0에서도 기본이 꺼져 있습니다.** 5단계에서 이렇게 켭니다.

```python
client_registration_options=ClientRegistrationOptions(enabled=True)
```

안 켜면 `/register` 가 404 라서 자기 문서가 없는 클라이언트는 등록할 길이 없습니다. 이 책의 Inspector 실습이 여기 걸립니다. claude.ai 는 자기 문서(CIMD)로 붙을 수 있어 조건이 맞으면 DCR 없이도 연결됩니다.

**사용자는 `claims` 로 갑니다 - 다만 이유가 3.4.7과 다릅니다.** 3.4.7에서는 접근 토큰의 `subject` 가 도구까지 오지 않아 `claims` 가 유일한 길이었습니다. **4.0에서는 실측 결과 `subject` 도 살아옵니다.** 그래도 이 코드는 `claims={"sub": user}` 를 유지합니다. 두 판 어디서나 동작하고, `sub` 이 표준이 정한 "누구" 자리이기 때문입니다.

---

## 3.4.7 → 4.0 실측 차이 요약

| 무엇 | 3.4.7 | 4.0.3 |
|---|---|---|
| 이 저장소 코드 | **50항목 통과** (2026-09-10 재확인. mcp 1.30.0) | **50항목 통과** (2026-09-10. mcp 2.1.1) |
| 도구 안의 stray `print` (stdio) | 표준 출력에 섞여 통신을 깨뜨림 | **stderr로 우회** - 안전 |
| 기반 SDK | mcp 1.30.0 | mcp 2.1.1 (공식 SDK v2) |
| `include={"authorization"}` 함정 | 있음 | **그대로 있음** |
| 등록 기본값 꺼짐 함정 | 있음 | **그대로 있음** |
| 접근 토큰 `subject` 소실 함정 | 있음 | **해소됨** (subject·claims 둘 다 생존) |
| `get_http_headers(include_all=True)` | 미검증 | 공식 인자로 확인 |
| CIMD(새 등록 방식) | 있음 (`fastmcp.server.auth.cimd`) | 있음. 직접 구현 제공자에는 어느 판에서도 자동 배선이 안 되어 `_enable_cimd_routes()`로 켭니다 |
| `httpx` 동반 설치 | 됨 | 안 됨 (점검용으로 별도 설치) |
| 폐기 경고 | - | 이 코드 기준 없음 |

---

## 라이선스

코드는 MIT 라이선스입니다. 쓰시고 고치시고 여러분 서비스에 넣으셔도 됩니다. 저작권 표시만 남겨 주세요. 전문은 `LICENSE` 파일에 있습니다.

**책 본문은 여기 포함되지 않습니다.** 이 저장소가 담는 것은 예제 코드뿐입니다.
