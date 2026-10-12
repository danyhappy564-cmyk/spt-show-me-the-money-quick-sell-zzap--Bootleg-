### ⚠️ IMPORTANT NOTICE / DISCLAIMER

- **Original Author:** swiftxp-hub (4.1 port reference: mattpsvreis)
- **Original Repository:** spt-show-me-the-money-quick-sell
- **Original Link:** https://github.com/swiftxp-hub/spt-show-me-the-money-quick-sell
- **License:** See upstream repository
- **This Port By:** R_F (danyhappy564-cmyk) — unofficial, AI-assisted port. Not affiliated with or endorsed by the original author.

1. **Reflection & Take-Downs:** I deeply reflect on the ECOT incident. As an AI-assisted "vibe coder," I will immediately delete files if the original authors ask.
2. **No Re-Distribution:** These ported builds are unverified, temporary fixes. Please do NOT re-upload or share them anywhere else.
3. **Do Not Pester Original Authors:** Never report bugs or pester original modders regarding issues from my unofficial ports.
4. **Full Credit & Respect:** I will always credit original creators on GitHub and prioritize their decisions above all else.
5. **Support Original Creators:** Instead of using my ports, please visit the original authors' Forge pages to leave kind words or tips.

---

## 변경 이력

- 2026-10-13 04:46 — README 맨 위 원작자 표기를 한 줄씩 나눠 보기 쉽게 정리 (내용 변화 없음)

# Show Me The Money: Quick Sell — 포크 변경점

원작: [swiftxp-hub/spt-show-me-the-money-quick-sell](https://github.com/swiftxp-hub/spt-show-me-the-money-quick-sell)
(단축키 + 마우스 클릭으로 아이템을 상인/플리에 즉시 판매하는 Show Me The Money 애드온)
4.1 포팅 참조: [mattpsvreis/spt-show-me-the-money-quick-sell](https://github.com/mattpsvreis/spt-show-me-the-money-quick-sell)

이 포크에서 손댄 내용만 기록합니다. 현재 기준 **SPT 4.1**.

---

## 빌드

**Show Me The Money 본체를 먼저 빌드하세요.** 본체 빌드가 자기 자신을 설치 폴더로
복사하고, 이 프로젝트는 그 설치본을 참조합니다.

```
dotnet build
```

경로는 `Directory.Build.props`의 `SptRoot`에서 나옵니다. 우선순위는
`Directory.Build.props.user` → `-p:SptRoot=...` → 환경변수 `SPT_ROOT` → 기본값
`E:\SPT 4.1`. `Directory.Build.targets`가 그 경로를 검증합니다.

본체 DLL 위치는 `ShowMeTheMoneyPluginPath`로 따로 덮어쓸 수 있습니다
(기본값 `$(SptRoot)\BepInEx\plugins\com.swiftxp.spt.showmethemoney`).

빌드 결과는 `$(SptRoot)\BepInEx\plugins\com.swiftxp.spt.showmethemoney.quicksell\`로
바로 복사됩니다.

---

<26/08/31 상세 변경점>

**빌드** — 새로 클론했을 때 빌드가 안 되던 원인 두 가지를 정리했습니다.

- **게임 어셈블리 참조 경로 하드코딩 제거** — 참조가 레포 3단계 위의
  `spt4-shared-dlls` 폴더를 가리키고 있었는데, 이건 수동으로 채워 넣어야 하는
  폴더라 새 클론에서는 존재하지 않고, 없으면 모든 참조가 미해결 상태가 됩니다.

  SPT 설치 폴더에서 가져오도록 변경. `SPTPath` 속성(기본값 `E:\SPT 4.0.10`,
  `-p:SPTPath=...`로 오버라이드 가능)을 통해 참조합니다.

- **Show Me The Money 본체 참조 방식 변경** — 옆에 있는 클론의 `bin` 폴더를
  참조하고 있었습니다. 즉 이 레포 바로 옆에 정확히 `spt-show-me-the-money`라는
  이름으로 본체 레포가 있고, 그게 Release로 빌드까지 되어 있어야 한다는 전제였는데,
  다른 폴더 구성이면 성립하지 않습니다.

  설치된 플러그인에서 가져오도록 변경했습니다. 본체를 먼저 빌드하면(본체 빌드가
  자기 자신을 설치본으로 복사합니다) 그걸로 끝입니다.

- **버전 선언 (2.3.0)** — 버전이 선언되어 있지 않아서 직접 빌드한 DLL이 자신을
  1.0.0으로 보고했습니다. 이 소스가 대응하는 릴리스 빌드에 맞춰 2.3.0으로 명시.

- 빌드된 플러그인을 설치본 plugins 폴더로 자동 복사(경로가 없으면 건너뛰므로 다른
  환경에서도 빌드는 성공).

- `Assets/icon.png` 삭제 (레포 용량 정리)

---

## 관련 사항 (본체 레포에서 수정)

Quick Sell을 쓰다 마주칠 수 있는 문제 두 가지는 본체
[spt-show-me-the-money](https://github.com/danyhappy564-cmyk/spt-show-me-the-money)
포크 쪽에서 고쳤습니다. 자세한 내용은 그쪽 README 참고.

- **인벤토리 클릭이 전부 먹통이 되던 문제** — 배포판 Quick Sell이 호출하는
  `PluginContextDataHolder`가 본체 소스 트리에는 다른 이름으로 있어서, 본체를 직접
  빌드하면 Quick Sell의 `GridItemView.OnClick` 프리픽스가 해석에 실패하고 예외가
  원래 `OnClick`보다 먼저 빠져나갑니다. 결과적으로 아이템 검사도 컨테이너 열기도
  안 되는데, Quick Sell을 지우면 "고쳐져서" Quick Sell 버그로 보였던 건입니다.
  본체에서 옛 이름을 포워딩하도록 수정했습니다.

- **본체 버전 미선언으로 Quick Sell이 로드를 거부하던 문제** —
  `missing dependencies: com.swiftxp.spt.showmethemoney (v2.6.0 or newer)`.
  본체가 버전을 선언하지 않아 1.0.0으로 보고되던 것으로, 본체에서 2.7.0을
  선언하도록 수정했습니다.

- **플리 판매 즉시 정산**도 본체 서버 모드 쪽에 들어가 있습니다
  (`instant-flea-sell.json`으로 조절).

---

<26/09/07 상세 변경점>

- **SPT 4.1로 포팅.** mattpsvreis의 4.1.2 포팅을 머지로 받았습니다. 이 포크는 빌드
  설정과 README만 건드렸던 터라 코드 충돌은 없었고, 충돌 3개 전부 빌드 설정/문서였습니다.

  - 빌드 설정 — 업스트림의 `SptRoot`/`SptManaged`/`SptBepInEx` 체계와
    `Directory.Build.targets` 검증을 채택하고, 이 포크의 `Version` 선언(2.3.0)과
    설치 폴더 자동 복사를 그 위에 유지. 기본값만 `E:\SPT 4.1`로 추가해서 클론 직후에도
    `Directory.Build.props.user` 없이 빌드됩니다. 업스트림이 새로 추가한
    `0Harmony`/`UnityEngine.CoreModule` 참조도 같이 받았습니다

  - **Show Me The Money 본체 참조는 이 포크 방식을 유지.** 업스트림 4.1 포팅이 이걸
    다시 옆 클론의 `bin/Release/netstandard2.1/` 경로로 되돌려놨는데, 이건 이 레포
    바로 옆에 정확히 `spt-show-me-the-money`라는 이름의 클론이 있고 Release로 빌드까지
    되어 있어야 성립하고, 아니면 조용히 미해결로 남습니다. 설치된 플러그인을 참조하는
    쪽(`ShowMeTheMoneyPluginPath`)이 본체 빌드가 자기를 설치본에 복사하는 동작과도
    맞아서 그대로 뒀습니다

- 본체 쪽 `PluginContextDataHolder` 호환 shim은 계속 남겨뒀습니다. 이 레포를 같이
  4.1로 빌드하면 필요 없지만, 배포본 Quick Sell 2.3.0을 쓰는 경우를 위해 유지
