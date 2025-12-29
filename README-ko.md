# GitHub Copilot Agent 설정

이 저장소는 제가 VS Code에서 GitHub Copilot Agent를 사용할 때 설정하는 구성입니다. \
VS Code에서 GitHub Copilot을 처음 사용하시는 분들께는 유용한 시작점이 될 수 있습니다. \
이 구성은 일반적인 개발 작업에서 사용자 입장에서의 제로샷 프롬프팅에 대한 효과적인 응답을 위한 지침입니다.

## 개요

- `.github`
  - [.github/agents](.github/agents): 커스텀 에이전트
  - [.github/instructions](.github/instructions): 커스텀 지침
  - [.github/skills](.github/skills): 에이전트 스킬
  - [.github/copilot-instructions.md](.github/copilot-instructions.md): 워크스페이스에 특화된 지침 작성
- `.vscode`
  - [.vscode/mcp.json](.vscode/mcp.json): 기본 MCP 서버 구성
  - [.vscode/settings.json](.vscode/settings.json): 기본 VS Code 설정

## 환경

- **VS Code**: 버전 1.108.x
- 필요 MCP 실행 커맨드: `npx`, `docker`, `uvx`
- 테스트한 모델: `Claude Opus 4.5`, `Claude Sonnet 4.5`, `GPT-5.2`

> **참고**: 사용하는 모델에 따라 작동 방식이 다를 수 있습니다. 각 모델은 지침과 컨텍스트를 다르게 해석할 수 있습니다.

## 설정 파일

### 커스텀 지침

- [copilot-agent.instructions.md](.github/instructions/copilot-agent.instructions.md): SSOT 우선순위를 포함한 6단계 실행 프로토콜, 품질 기준, 가정 금지 규칙
- [subagents.instructions.md](.github/instructions/subagents.instructions.md): 금지된 MCP 호출, 위임 규칙, Context Package 계약을 정의하는 SSOT
- [taming-copilot.instructions.md](.github/instructions/taming-copilot.instructions.md): 사용자 지시 우선, 요청 시에만 코드 제공, 직접적이고 간결한 응답 철학
- [task-direction-approval.instructions.md](.github/instructions/task-direction-approval.instructions.md): 기술/아키텍처 변경 전 사용자 동의 필수, 실패 시 옵션 제시 프로토콜

### 에이전트 스킬

- [investigation-mode](.github/skills/investigation-mode/SKILL.md): 2회 이상 연속 실패 시 구현 중단, 근본 원인 분석 후 재개하는 워크플로우
- [minimalist-surgical-development](.github/skills/minimalist-surgical-development/SKILL.md): 최소 diff 우선, 표준 라이브러리 선호, 불필요한 리팩토링 금지
- [root-cause-tracing](.github/skills/root-cause-tracing/SKILL.md): 콜스택을 역추적하여 증상이 아닌 원점(트리거)을 찾는 디버깅 방법론
- [task-direction-approval](.github/skills/task-direction-approval/SKILL.md): 실패 원인 설명, 2-3개 옵션과 트레이드오프 제시, 사용자의 명시적 선택 대기
- [uncertainty-verification](.github/skills/uncertainty-verification/SKILL.md): 정확한 명령어/설정/API는 공식 문서로 검증 필수 - 가정 기반 답변 금지
- [verification-before-completion](.github/skills/verification-before-completion/SKILL.md): 완료 주장 전 검증 명령어 실행 및 결과 확인 필수 - 증거가 주장보다 먼저

### 커스텀 에이전트

- [critical-thinking.agent.md](.github/agents/critical-thinking.agent.md): 전제를 질문하고 최적의 해결책 도출 지원
- [mentor.agent.md](.github/agents/mentor.agent.md): 비판적 질문으로 엔지니어 성장 지원 및 멘토링 제공

## VS Code 문서

- [커스텀 지침](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)
- [커스텀 에이전트](https://code.visualstudio.com/docs/copilot/customization/custom-agents)
- [프롬프트 파일](https://code.visualstudio.com/docs/copilot/customization/prompt-files)
- [에이전트 스킬](https://code.visualstudio.com/docs/copilot/customization/agent-skills)

## Links

- [Awesome GitHub Copilot](https://github.com/github/awesome-copilot)
- [Spec-Driven Development](https://github.com/github/spec-kit)
- [GitHub Copilot CLI](https://github.com/github/copilot-cli)
- [GitHub Agentic Workflows](https://github.com/githubnext/gh-aw)
