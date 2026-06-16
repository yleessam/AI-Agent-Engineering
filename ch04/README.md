# Chapter 04: 도구 사용 (Tool Use) 및 MCP

이 디렉터리는 LLM이 외부 도구를 사용하는 방법과 MCP(Model Context Protocol)를 활용하는 예제를 다룹니다.

## 파일 목록

- `calculator_tool_use.py`: 사칙연산 도구를 정의하고 LLM이 이를 활용하여 계산 문제를 해결하는 예제입니다.
- `wikipedia_tool_use.py`: 위키피디아 검색 도구를 사용하여 정보를 조회하는 예제입니다.
- `stock_price_tool_use.py`: 주식 가격 정보를 조회하는 도구 사용 예제입니다.
- `langgraph_mcp_client.py`: LangGraph 기반 에이전트가 MCP 서버(수학, 날씨)를 도구로 활용하는 클라이언트 예제입니다.
- `mcp_servers/MCP_math_server.py`: 수학 연산을 처리하는 MCP 서버입니다. (stdio 방식)
- `mcp_servers/MCP_weather_server.py`: 날씨 정보를 제공하는 MCP 서버입니다. (HTTP 방식)

## 환경 설정

프로젝트 루트에 `.env` 파일을 생성하고 API 키를 설정합니다.

```
OPENAI_API_KEY=sk-...
```

## 실행 방법

### 일반 도구 예제

프로젝트 루트(`AI-Agent-Engineering/`) 또는 `ch04/` 디렉터리에서 실행합니다.

```bash
# 계산기 도구 예제
python calculator_tool_use.py

# 위키피디아 도구 예제
python wikipedia_tool_use.py

# 주식 가격 도구 예제
python stock_price_tool_use.py
```

### LangGraph + MCP 클라이언트 예제

MCP 서버는 두 가지 통신 방식을 사용합니다.

| 서버                  | 방식  | 별도 실행 필요                   |
| --------------------- | ----- | -------------------------------- |
| MCP_math_server.py    | stdio | 불필요 (클라이언트가 자동 실행)  |
| MCP_weather_server.py | HTTP  | 필요 (별도 터미널에서 먼저 실행) |

**터미널 1 — weather 서버 먼저 실행**

```bash
cd AI-Agent-Engineering/ch04
python mcp_servers/MCP_weather_server.py
```

아래 메시지가 출력되면 서버가 정상 실행된 것입니다.

```
Starting MCP Weather Server on http://0.0.0.0:8000
MCP endpoint: http://0.0.0.0:8000/mcp
INFO: Uvicorn running on http://0.0.0.0:8000
```

서버를 종료하지 말고 터미널을 그대로 유지합니다.

**터미널 2 — 클라이언트 실행**

반드시 프로젝트 루트(`AI-Agent-Engineering/`)에서 실행해야 합니다.
math 서버의 경로(`ch04/mcp_servers/MCP_math_server.py`)가 루트 기준으로 설정되어 있기 때문입니다.

```bash
cd AI-Agent-Engineering
python ch04/langgraph_mcp_client.py
```

정상 실행 시 출력 예시:

```
Math answer: [{'type': 'text', 'text': '계산 결과: 96', ...}]
Weather answer: [{'type': 'text', 'text': '뉴욕의 현재 날씨: 화씨 58도 (섭씨 14도), 맑음', ...}]
```

## 주의 사항

- `langgraph_mcp_client.py`의 weather URL이 `http://0.0.0.0:8000/mcp`로 설정된 경우 `http://127.0.0.1:8000/mcp`로 변경해야 합니다.
- `wikipedia_tool_use.py` 실행 전 User-Agent를 설정해야 Wikipedia API 403 오류를 방지할 수 있습니다.

```python
import wikipedia
wikipedia.set_user_agent("AI-Agent-Engineering/1.0 (study purpose)")
```
