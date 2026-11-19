# Microsoft Ignite 2026 AI Infrastructure レポート

## AI infrastructure from cloud to edge

- **日時：** 2026年11月18日（水）7:00〜8:00 JST
- **セッションID：** INV643
- **登壇者：** Mark Russinovich、Pavan Davuluri
- **公式動画：** [Microsoft Events 公開動画](https://www.youtube.com/watch?v=I27UYrukuAw)（1時間1分49秒）
- **確認日：** 2026年11月19日

本レポートは筆者がセッションを視聴したメモからAIによる補足情報を追記し整理したものである。

## 要点

**AIをクラウド・オンプレミス・エッジ・PCで実行し、データの所在、性能、コスト、管理要件に応じて配置を選ぶための基盤を紹介したセッション。** 前半はAzureのAI基盤とアプリケーション刷新、後半はWindowsのセキュリティとローカル・クラウドを組み合わせるHybrid Intelligenceを扱う。[Azureの公式発表](https://azure.microsoft.com/en-us/blog/introducing-azure-catalyst-agentic-modernization-and-new-ai-infrastructure-innovations/)、[Windowsの公式発表](https://blogs.windows.com/windowsexperience/2026/11/17/microsoft-ignite-2026-building-windows-for-the-next-era-of-work/)

## 1. モデルとエージェントを支えるAzureの基盤

AI向けの基盤として、ストレージからGPUへのRDMA転送、ハードウェアによる鍵保護に加え、エージェントの実行・通信・状態を管理する仕組みを紹介した。

| 仕組み | 役割 |
| --- | --- |
| Agent Grid | IDに基づいて、エージェントの接続先や利用可能なリソースをネットワーク側で制御 |
| Context Cache | 計算済みのコンテキストをAzure Storageに保持し、再計算や推論再開時の負担を軽減 |
| Azure Container Instancesのエージェント用サンドボックス | 軽量VMによる隔離と高速な起動で、エージェントのコード実行を支える |

AKS向けにも、生成コードをVMレベルで隔離するAgent Sandbox extensionを展開する。実行環境の隔離と、Agent Gridによる通信制御を組み合わせる方向性が示された。[Azure AI基盤の公式発表](https://azure.microsoft.com/en-us/blog/introducing-azure-catalyst-agentic-modernization-and-new-ai-infrastructure-innovations/)

## 2. AKS AI Manager for Foundry と工場デモ

**Foundryから、自社管理のAKSへ対応するオープンウェイトモデルとFoundry hosted agentsを配備できる。両機能とも限定プレビュー。** 開発チームはFoundryの操作を使い、基盤チームはネットワーク・ID・ポリシー・GPU容量を管理する。モデルの要件やGPUの空き容量を踏まえた配置最適化も支援する。[AKSの公式発表](https://techcommunity.microsoft.com/blog/appsonazureblog/run-models-and-agents-on-your-own-kubernetes-clusters-aks-at-microsoft-ignite-20/4573912)

工場の短時間停止を調査するデモでは、次の流れを紹介した。

1. 基盤チームが用意したAKSクラスターをFoundryに接続する。
2. FoundryでLlama 3.3 70Bを選び、AKSを実行先として配備する。
3. 工場内のGPU・カメラ付きノードを**AKS Flex Node**として接続し、現場の画像と設備情報を調査に利用する。

デモシナリオでは、調査時間を約2時間から4分へ短縮したと説明された。

Flex nodes for AKSはプレビュー。Azure外の対応ノードもAKSの計算資源として利用する仕組みであり、クラウドのFoundry・AKSと現場の計算資源をつなぐ構成として紹介された。[公式発表](https://azure.microsoft.com/en-us/blog/introducing-azure-catalyst-agentic-modernization-and-new-ai-infrastructure-innovations/)

## 3. ★Azure Catalyst による移行とモダナイゼーション

**Azure MigrateとGitHub Copilot modernizationを統合し、資産把握から設計・実行・検証までをつなぐ。** アプリケーション、データ、インフラの依存関係を共有し、基盤担当者・DB管理者・開発者が共通の計画で作業できる。提供状況は機能ごとに異なり、今回の追加機能にはパブリックプレビューが含まれる。[Azure Catalystの公式発表](https://techcommunity.microsoft.com/blog/appsonazureblog/azure-catalyst-one-connected-agent-led-modernization-experience-across-apps-data/4573988)

**デモではServiceNowの資産情報を取り込み、依存関係とコストを評価して移行先を設計。** Web層をApp Service、DBをAzure SQL Database Hyperscale、ファイルをAzure Filesへ配置し、DB管理者の作業とGitHub Copilotによるコード改修・既存CI/CDでの検証を一連の流れに組み込んだ。

Columbiaの事例では、AIによる設計案の比較やコード改修が、技術的負債への対応と変更の迅速化につながったと紹介された。

★進化版Azure Migrateという感じ

## 4. Confidential Computing とAzure Local

**Confidential Computingで処理中のデータを保護し、Sovereign Cloudで実行場所やデータの管理方法を選ぶ。** セッションでは、VM・コンテナー・データサービスにまたがる機密コンピューティングの拡張を説明した。[22:58〜25:41](https://www.youtube.com/watch?v=I27UYrukuAw&t=1378s)

Azure Localのデモでは、パブリックAzureとの接続を切った環境で、Foundry LocalとローカルSharePointの文書を使う研究支援エージェントを紹介した。

提供状況は次のように分かれる。

- **Foundry Local on Azure Localのモデル推論：** 一般提供。接続・切断環境で利用できる。
- **エージェントサービスとガードレール：** プレビュー。
- **既存の接続環境から切断環境への管理切り替え：** プレビュー。

工場の低遅延処理や、データを自社環境内で扱う要件に応じて検討する基盤となる。[Sovereign Private Cloudの公式発表](https://techcommunity.microsoft.com/blog/azurearcblog/whats-new-with-sovereign-private-cloud-at-microsoft-ignite-2026/4573886)

## 5. Windowsのセキュリティとエージェントの隔離

Windows側では、端末の保護・更新管理を整えたうえで、エージェントにも独立したIDとアクセス制御を適用する考え方を説明した。

| 機能 | 提供状況 | ポイント |
| --- | --- | --- |
| Windows endpoint security platform | 一般提供 | セキュリティ製品の重要な保護処理をカーネルモードからユーザーモードへ移し、障害の影響を抑える |
| Windows administrator protection | 一般提供 | 通常は標準ユーザー権限で動作し、必要な作業に対して本人確認後に管理者権限を付与 |
| Windows baseline protection | プレビュー | Secure Boot、仮想化ベースのセキュリティ、メモリ整合性などをまとめて確認 |
| Microsoft Execution Containers（MXC） | Windows 11では一般提供済み | エージェントごとに実行環境を隔離し、ファイル・ネットワークへのアクセスを制限 |

Windows AutopatchやIntuneによる更新管理、宣言的な設定管理も紹介された。[Windowsの公式発表](https://blogs.windows.com/windowsexperience/2026/11/17/microsoft-ignite-2026-building-windows-for-the-next-era-of-work/)、

**MXCのデモでは、契約書の要約をアクセス範囲の狭いプロセスコンテナーで実行し、リスクの高い検査にはローカルMicro VMを使う構成を提示。** 作業に応じた隔離方式の自動選択は今後の方向性として説明された。。Micro VM機能は公式発表では実験段階とされている。[Windowsの公式発表](https://blogs.windows.com/windowsexperience/2026/11/17/microsoft-ignite-2026-building-windows-for-the-next-era-of-work/)

Agent 365、Entra、Intuneなどと連携し、エージェントのID・権限・動作を組織全体で管理する。[Windowsのエージェント管理に関する公式説明](https://blogs.windows.com/windowsdeveloper/2026/11/17/enterprise-deployment-of-governed-agents-on-windows/)

## 6. Hybrid Intelligence と実行場所の選択

### PC上のCopilotとGitHub Copilot Fusion

Copilot+ PC向けには、利用者の許可を得てローカルの情報を参照し、Windows上の操作やローカルモデルを利用する機能を提供予定。デモでは音声出力先の診断と、ダウンロードした文書のOneDriveへの整理を紹介した。

**GitHub Copilot Fusionのローカルモデル自動選択はプレビュー。** 対応PCの計算資源を使い、コーディング作業をローカル・クラウドのモデルへ振り分ける。Copilotアプリ、CLI、VS Codeで利用できるが、振り分け判断にはクラウド接続が必要となる。[GitHubの公式説明](https://github.blog/changelog/2026-10-28-github-copilot-fusion-now-supports-selection-of-local-models/)、

### Cloud PCとWindows Server

| 選択肢 | 提供状況と用途 |
| --- | --- |
| [Windows 365 for Agents](https://techcommunity.microsoft.com/blog/windows-itpro-blog/windows-365-at-ignite-2026-introducing-new-cloud-pc-capabilities-for-people-agen/4570006) | 一般提供。エージェント専用のCloud PCを管理し、GUIを使う業務を自動化。Copilot Studioで利用でき、Foundry連携は2027年初めにプレビュー予定 |
| [Windows 365のDirect Stream](https://techcommunity.microsoft.com/blog/windows-itpro-blog/windows-365-at-ignite-2026-introducing-new-cloud-pc-capabilities-for-people-agen/4570006) | Cloud PCの接続・描画を改善する技術。GPU向けの描画強化は2027年初めにパブリックプレビュー予定 |
| [Windows Server 2028](https://techcommunity.microsoft.com/blog/WindowsServerNewsandBestPractices/windows-server-2028-a-trusted-foundation-for-an-evolving-enterprise/4574112) | パブリックプレビュー。サーバーの保護と管理を強化し、Foundry連携によるローカル推論へ拡張。Hybrid Intelligence機能は対象顧客を限定した早期アクセスで、ハードウェア要件がある |

## 7. PwCの事例と設計・運用への示唆

PwCは、コンサルタントと開発者では必要なAI処理が異なるため、**業務・ハードウェア・ソフトウェアを一緒に設計する**方針を説明した。端末とクラウドへの配置を整理し、共通の利用体験を維持する一方、モデル・バージョン・評価の管理は今後も取り組む課題として挙げた。

本セッションからは、AIの配置をGPU性能だけで決めず、**データの管理、遅延、費用、エージェントの権限、継続的な運用まで含めて選ぶことが重要**と読み取れる。基盤チームと開発チームの役割を明確にし、業務ごとにクラウド・自社基盤・PCの使い分けを検証する進め方が考えられる。

