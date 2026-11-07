<div align="center">

<img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/banner.png" width="100%" alt="EchoPort: Beyond Lock-In. 어떤 클라우드든, 막힘없이." />

[app.echoport.uk](https://app.echoport.uk) · [API 문서](https://api.echoport.uk/docs)

<sub>Team Echo · SoftBank Hackathon 2026 in Korea 본선 · 日本語はページ下部にあります</sub>

</div>

---

## 무엇을 만드나요

EchoPort는 AWS · GCP · 온프레미스 클러스터를 한 화면에서 관리하고, 재난이 오기 전에 가동 중인 서비스를 멈추지 않고 안전한 환경으로 옮겼다가 되돌리는 멀티 클라우드 관리 플랫폼이에요.

사용자가 접속하는 주소는 그대로예요. 그 뒤에서 트래픽을 10 → 50 → 100%로 옮기고, 단계마다 도착 환경의 요청 · 헬스체크 · 복제 지연을 확인해요. 문제가 생기면 자동으로 원래 환경으로 되돌려요.

<p align="center">
  <img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/screens/migration.png" width="900" alt="이전 결과 화면: AWS 도쿄에서 GCP 서울로 11초, 판정한 요청 149건 중 실패 0건" />
  <br/><sub>운영 긴급 이전 결과: AWS 도쿄 → GCP 서울 11초, 판정한 요청 149건 중 실패 0건</sub>
</p>

## 왜 필요한가요

- 한 지역에 묶이면 함께 멈춰요. 재난이나 공급자 장애가 나면 그 지역의 서비스가 한꺼번에 영향을 받아요.
- 상용 도구는 경계 안에서만 전환해요. 한 플랫폼 · 한 클러스터 · 한 클라우드 안의 전환은 잘 되지만, 클라우드 경계를 넘어 트래픽 · 데이터 · 롤백을 한 흐름으로 묶은 서비스는 저희 조사 범위에서 찾지 못했어요.
- 옮긴 뒤에도 지켜야 해요. 환경이 달라도 같은 보안 기준을 지키는지, 누가 무엇을 승인했는지 남아야 해요.

### 기존 서비스에서 가져온 것과 다른 점

| 기능 | 참고한 서비스 | EchoPort가 다른 점 |
|---|---|---|
| 배포 | Railway · Northflank | 클라우드 + 온프레미스, 예비 환경이 같은 버전을 따라가요 |
| 고정 진입점 | Railway · Argo Rollouts | 두 클라우드 앞의 Envoy, DNS TTL을 기다리지 않아요 |
| 단계 전환 | Argo Rollouts | 클라우드 경계를 넘는 비율 전환, 요청 건수로 판정해요 |
| 데이터 이전 | AWS DMS · Google DMS | 공급자에 묶이지 않는 PostgreSQL 논리 복제 + PgBouncer |
| 계획 · 승인 | Railway · IaC plan/apply | 계획 뒤 환경이 바뀌면 낡은 계획으로 거부해요 |
| 보안 | Wiz · SCC · Defender | 같은 규칙을 전 환경에 적용하고, 게이트가 이전을 막아요 |
| 에이전트 | E2B · Railway Agent | 격리 샌드박스 + 사람 승인, 운영은 읽기만 해요 |

## 주요 기능

### 원터치 배포
저장소 주소만 넣으면 서비스가 등록되고 배포돼요. 한 번 빌드한 이미지를 같은 digest로 옮기고, 예비 환경은 트래픽 0%로 같은 버전을 따라가요. 실패하면 단계 · 원인 · 로그를 보여 주고, 범위를 확인한 뒤 되돌려요.

### 무중단 이전
승인된 계획이 진입점 가중치를 10 → 50 → 100%로 옮겨요. 단계마다 도착 환경의 요청과 5xx를 판정하고, 실패하면 자동으로 되돌려요.

### 재난 대피와 복귀
진원지와 규모로 영향 반경을 계산하고, 안전 · 용량 · 비용 · 복귀 시간 · 데이터 위치로 대피 후보를 골라요. 재난이 끝나지 않은 지역으로는 돌아갈 수 없어요. USGS 지진 피드도 연결할 수 있어요.

### AI 사전 점검과 샌드박스
이전 전에 두 환경을 규칙과 AI로 비교해요. AI는 경고만 낼 수 있고 통과시킬 수 없어요. 사람이 승인한 패치만 격리 샌드박스에서 적용 · 빌드 · 스캔 · 헬스체크해요.

### 보안 게이트
Trivy · TLS 시험 · Kubernetes · 클라우드 API · CI · 감사 로그의 증거로 18개 통제 × 3개 환경을 판정해요. 기한이 있고 사람이 승인한 예외만 실패를 면제해요. 가짜 증거는 판정에 쓰지 않아요.

### 권한과 감사
뷰어 · 개발자 · 어드민 역할, 에이전트 전용 토큰, 사람 · 에이전트 · 시스템의 행동을 모두 남기는 감사 로그가 있어요. 권한이 없는 버튼은 숨기지 않고 이유를 보여 줘요.

## 서비스 화면

<table>
<tr>
<td width="50%"><img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/screens/landing.png" alt="EchoPort 소개 화면" /><br/><sub><b>소개</b>: 지구본 위에 환경을 보여 줘요</sub></td>
<td width="50%"><img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/screens/overview.png" alt="서비스 개요 화면: 지표와 토폴로지" /><br/><sub><b>서비스 개요</b>: 지표와 환경별 트래픽 토폴로지</sub></td>
</tr>
<tr>
<td width="50%"><img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/screens/migration.png" alt="이전 결과 화면" /><br/><sub><b>이전 결과</b>: 단계별 판정과 RTO</sub></td>
<td width="50%"><img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/screens/security.png" alt="보안 매트릭스 화면" /><br/><sub><b>보안 매트릭스</b>: 환경별 판정과 증거</sub></td>
</tr>
</table>

<sub>운영 화면이에요. 서비스 주소 일부는 가렸어요.</sub>

## 디자인

<p align="center">
  <img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/design.png" width="100%" alt="EchoPort 디자인: 로고, 색, 라이트 · 다크 · 프로젝터 테마, 글꼴" />
</p>

- 로고: 워드마크 PORT의 O는 두 지점을 호로 잇는 지구본이에요. 환경 사이를 옮겨 가는 서비스를 뜻해요.
- 색은 의미로만 써요. 브랜드색은 주 버튼과 선택 상태에만 쓰고, 성공 · 주의 · 위험 · 이전 중 · AI는 각자 색이 있어요. 색만으로 상태를 전하지 않고 항상 글자를 같이 둬요.
- 테마 3개: 라이트 · 다크 · 프로젝터. 프로젝터는 무대 화면용으로 대비가 높고 글자가 한 단계 커요.
- 토큰 하나로 맞춰요. 색 · 간격 · 글자 · 반경 · 그림자는 Figma 변수에서 뽑은 `--ep-*` 토큰만 써요.

## 아키텍처

<p align="center">
  <img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/architecture.svg" width="100%" alt="EchoPort 시스템 구성: Cloudflare, GCP 네트워크 LB, Envoy 진입점, AWS 도쿄, GCP 서울, 온프레미스, PostgreSQL 복제, 서울의 관리 평면" />
</p>

- 주소는 고정이에요. `{service}.echoport.uk` → Cloudflare → GCP 네트워크 LB → Envoy 진입점 a · b
- 관리 평면은 재난 영향권 밖에 둬요. API와 AI 샌드박스는 서울에 있어요. 시연 재난 지역은 도쿄예요.
- 온프레미스는 인바운드 포트를 열지 않아요. Cloudflare Tunnel로 연결해요.
- 비밀값은 위치만 저장해요. 값은 GCP Secret Manager에 두고, CI는 OIDC를 써서 클라우드 키를 저장하지 않아요.

## 이전 흐름

1. 감지: 재난 시뮬레이션 또는 지진 피드로 영향 환경을 판정해요
2. 대피처 선택: 후보 점수와 근거를 보고 도착 환경을 골라요
3. AI 사전 점검: 샌드박스에서 두 환경을 비교하고 패치를 제안해요
4. 승인: 보안 게이트가 통과해야 사람이 승인할 수 있어요
5. 이전: 트래픽 10 → 50 → 100%, 단계마다 판정, 실패하면 자동 롤백
6. DB 쓰기 전환: 쓰기를 멈추고 복제를 따라잡은 뒤 서울 DB를 승격해요
7. 복귀: 재난이 끝나면 역방향 복제로 원래 환경에 돌아와요

## 실제로 해 본 결과

실제로 잰 값만, 어디서 쟀는지와 함께 적었어요.

| 항목 | 결과 | 측정 |
|---|---|---|
| 긴급 이전 | AWS 도쿄 → GCP 서울 11초, 판정한 요청 149건 중 실패 0건 | 운영 · 11/7 01:21 |
| 실패 주입 자동 롤백 | 1.5초 | 리허설 · 시험용 실행기 |
| DB 쓰기 전환 | 쓰기 정지 7.97초, A 168행 = B 168행 (누락 0 · 중복 0) | 운영 리허설 · 11/4 · 초당 10건 쓰기 |
| DB 복귀 | 9.42초, 누락 0 · 중복 0 | 운영 리허설 · 11/4 |
| 진입점 장애 전환 | VM a → b 3.8초 (실패 요청 12건) | 11/6 |

## 한계와 다음 과제

- 쓰기 정지가 목표보다 길어요. 설계 목표는 1초였고 실측은 약 8초예요. 역방향 구독을 미리 만들어 두는 안을 검토하고 있어요.
- 쓰기 부하 측정이 7.97초 리허설 1회뿐이에요. 이때 쓰기 요청 166건 중 5건은 클라이언트 10초 타임아웃이 났고, 그중 4건은 DB에 반영됐어요.
- 갑작스러운 장애에서는 마지막 쓰기를 잃을 수 있어요. 복제가 비동기라 RPO가 0이 아니에요. 진입점 기록으로 최근 쓰기를 다시 재생해 줄여요.
- 재난은 시뮬레이션이에요. 시뮬레이션이어도 그 재난이 시작한 이전은 실제예요. 지진 피드는 기본으로 꺼져 있어요.
- 온프레미스는 아직 Envoy 가중치 전환 대상이 아니에요. 진입점 두 대도 모두 서울에 있어요.
- 배포 실패 AI 진단은 러너 연결 전이라 가짜 결과예요. 화면에 `simulated` 배지로 밝혀요.

## 저장소

| 레포 | 설명 |
|---|---|
| [echo-frontend](https://github.com/Softbank-Hackathon-2026-Team-Echo/echo-frontend) | 웹 콘솔: 대시보드 · 서비스 작업 공간 · 대피 · 이전 · 보안 화면 |
| [echo-was](https://github.com/Softbank-Hackathon-2026-Team-Echo/echo-was) | 관리 평면 API: 환경 · 배포 · 이전 계획 · 재난 · 보안 게이트 · 감사 |
| [echo-infra](https://github.com/Softbank-Hackathon-2026-Team-Echo/echo-infra) | 인프라 · 이전 엔진: Terraform · EKS · GKE · k3s · 진입점 · 데이터 복제 |
| [echo-ai-agent](https://github.com/Softbank-Hackathon-2026-Team-Echo/echo-ai-agent) | AI 에이전트 러너: 격리 샌드박스에서 점검 · 수정 · 검증 |
| [echo-demo-app](https://github.com/Softbank-Hackathon-2026-Team-Echo/echo-demo-app) | 시연용 관리 대상 앱: 순번 쓰기로 이전 중 누락 · 중복을 드러내요 |
| [echo-demo-db](https://github.com/Softbank-Hackathon-2026-Team-Echo/echo-demo-db) | 요청 기록 재생 시연 앱: 원본 DB가 죽어도 주문이 이어지는지 보여 줘요 |
| [echo-secure](https://github.com/Softbank-Hackathon-2026-Team-Echo/echo-secure) | 보안 · 거버넌스 안내: 실제 코드는 echo-was · echo-infra에 있어요 |
| [.github](https://github.com/Softbank-Hackathon-2026-Team-Echo/.github) | 이 소개 페이지 |

## 기술 스택

| 영역 | 스택 |
|---|---|
| Web | Vite · React 19 · TypeScript · TanStack Query · Tailwind CSS v4 · shadcn/ui · React Flow + elkjs · d3-geo · i18next (English · 한국어 · 日本語) · Sentry · Cloudflare Pages |
| API | Python 3.13 · FastAPI · Pydantic v2 · SQLAlchemy 2.0 · PostgreSQL 17 · Alembic · GKE · GitHub App |
| Infra | Terraform · EKS · GKE · k3s + Cloudflare Tunnel · Envoy · Prometheus · Grafana |
| Data | PostgreSQL 논리 복제 · PgBouncer · 진입점 요청 기록 재생 |
| AI | Claude API · Docker 샌드박스 · Trivy |
| Security | Trivy · Gitleaks · TLS 시험 · GCP Secret Manager · OIDC |

## 협업 방식

- 브랜치 + PR: `main`에 직접 push하지 않고, 이슈와 PR로 남겨요.
- 계약 우선: API는 서버 문서가 기준이에요. 필요한 칸은 `(가칭)`으로 요청하고 제공 측이 이름을 정해요.
- 결정 기록: 각 레포의 `CLAUDE.md`에 무엇을 왜 골랐는지 남겨요. 번복도 지우지 않아요.
- 목업은 목업이라고: 가짜 값은 화면에 `simulated` 배지를 달고, 재지 않은 값은 `—`로 비워요.

## 팀

<table>
<tr>
<td align="center" width="20%"><a href="https://github.com/kimdoyoung1110"><img src="https://github.com/kimdoyoung1110.png?size=200" width="96" alt="kimdoyoung1110" /></a><br/><b>김도영</b><br/><sub>팀장 · 프론트</sub></td>
<td align="center" width="20%"><a href="https://github.com/Saccharine1211"><img src="https://github.com/Saccharine1211.png?size=200" width="96" alt="Saccharine1211" /></a><br/><b>김지민</b><br/><sub>API 서버 · 시연 앱</sub></td>
<td align="center" width="20%"><a href="https://github.com/SeoDongOk"><img src="https://github.com/SeoDongOk.png?size=200" width="96" alt="SeoDongOk" /></a><br/><b>서동옥</b><br/><sub>인프라</sub></td>
<td align="center" width="20%"><a href="https://github.com/xtkww971"><img src="https://github.com/xtkww971.png?size=200" width="96" alt="xtkww971" /></a><br/><b>이도현</b><br/><sub>인프라 · 데이터</sub></td>
<td align="center" width="20%"><a href="https://github.com/hyeongrae-kim"><img src="https://github.com/hyeongrae-kim.png?size=200" width="96" alt="hyeongrae-kim" /></a><br/><b>김형래</b><br/><sub>보안 · AI</sub></td>
</tr>
</table>

---

<details>
<summary><h2>日本語</h2></summary>

<img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/banner.ja.png" width="100%" alt="EchoPort: Beyond Lock-In. クラウド間を、なめらかに。" />

## EchoPortとは

EchoPortは、AWS・GCP・オンプレミスのクラスターを一つの画面で管理し、災害が来る前に稼働中のサービスを止めずに安全な環境へ移し、また戻すマルチクラウド管理プラットフォームです。

ユーザーがアクセスするアドレスは変わりません。その裏でトラフィックを10 → 50 → 100%と移し、段階ごとに移行先のリクエスト・ヘルスチェック・レプリケーション遅延を確認します。問題があれば自動で元の環境に戻します。

## なぜ必要か

- 一つの地域に依存すると一緒に止まります。 災害や事業者の障害が起きると、その地域のサービスがまとめて影響を受けます。
- 既存ツールは境界の中でのみ切り替えます。 クラウドの境界を越えてトラフィック・データ・ロールバックを一つの流れにまとめたサービスは、私たちの調査範囲では見つかりませんでした。
- 移した後も守る必要があります。 環境が変わっても同じセキュリティ基準を守り、誰が何を承認したかを残す必要があります。

## 主な機能

- ワンタッチデプロイ: 一度ビルドしたイメージを同じ digest で移し、予備環境はトラフィック0%で同じバージョンに追従します
- 無停止移行: 重みを10 → 50 → 100%と移し、失敗すると自動で戻します
- 災害避難と復帰: 震源と規模から影響範囲を計算し、避難先の候補を選びます。災害が終わっていない地域には戻れません
- AI事前点検とサンドボックス: AIは警告のみ出せて、通過はさせられません。人が承認したパッチだけを隔離環境で適用・ビルド・スキャン・ヘルスチェックします
- セキュリティゲート: 証拠をもとに18統制 × 3環境を判定します。期限付きで人が承認した例外だけが失敗を免除します
- 権限と監査: 閲覧者・開発者・管理者の役割、エージェント専用トークン、すべての行動を残す監査ログ

## デザイン

<p align="center">
  <img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/design.png" width="100%" alt="EchoPort デザイン: ロゴ、色、ライト · ダーク · プロジェクターテーマ、フォント" />
</p>

- ロゴ: ワードマーク PORT の O は、二つの地点を弧で結ぶ地球儀です。環境の間を移るサービスを表します
- 色は意味にだけ使います。 ブランド色は主ボタンと選択状態のみで、状態は色と文字を必ず併記します
- テーマは3つ: ライト · ダーク · プロジェクター。プロジェクターはステージ画面用で、コントラストが高く文字が一段大きくなります
- トークンで統一します。 色 · 余白 · 文字 · 角丸 · 影は Figma 変数から出力した `--ep-*` トークンのみを使います

## アーキテクチャ

<p align="center">
  <img src="https://raw.githubusercontent.com/Softbank-Hackathon-2026-Team-Echo/.github/main/profile/assets/architecture.ja.svg" width="100%" alt="EchoPort システム構成: Cloudflare、GCP ネットワーク LB、Envoy エントリ、AWS 東京、GCP ソウル、オンプレミス、PostgreSQL レプリケーション、ソウルの管理プレーン" />
</p>

## 実際の結果

実際に測った値だけを、測定場所とともに記載しています。

| 項目 | 結果 | 測定 |
|---|---|---|
| 緊急移行 | AWS 東京 → GCP ソウル 11秒、判定したリクエスト149件中失敗0件 | 運用 · 11/7 01:21 |
| 障害注入の自動ロールバック | 1.5秒 | リハーサル · テスト用実行機 |
| DB書き込み切り替え | 書き込み停止 7.97秒、A 168行 = B 168行(欠落0 · 重複0) | 運用リハーサル · 11/4 · 毎秒10件の書き込み |
| DB復帰 | 9.42秒、欠落0 · 重複0 | 運用リハーサル · 11/4 |
| エントリ障害の切り替え | VM a → b 3.8秒(失敗リクエスト12件) | 11/6 |

## 制約と今後の課題

- 書き込み停止は設計目標の1秒より長く、実測は約8秒です
- 書き込み負荷をかけた測定は7.97秒のリハーサル1回のみで、166件中5件はクライアントの10秒タイムアウトになり、そのうち4件はDBに反映されました
- レプリケーションが非同期のため、突然の障害では最後の書き込みを失う可能性があります
- 災害はシミュレーションですが、その災害が始めた移行は実際の移行です。地震フィードは既定でオフです
- オンプレミスはまだ Envoy の重み切り替えの対象ではなく、エントリ2台はどちらもソウルにあります
- デプロイ失敗のAI診断はランナー接続前のため偽の結果で、画面に `simulated` バッジで明示します

## リンク

| | リンク |
|---|---|
| Web コンソール | [app.echoport.uk](https://app.echoport.uk) |
| API ドキュメント | [api.echoport.uk/docs](https://api.echoport.uk/docs) |

## チーム

キム・ドヨン(リーダー · フロントエンド) · キム・ジミン(APIサーバー · デモアプリ) · ソ・ドンオク(インフラ) · イ・ドヒョン(インフラ · データ) · キム・ヒョンレ(セキュリティ · AI)

</details>
