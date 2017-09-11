---
title: "データ保護の概要"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4542cd37-b47c-454c-be19-d1b5810d67fe
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/introduction
ms.openlocfilehash: bcf1ce5a272a374c9605e50dee5c5fb27305527d
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-data-protection"></a>データ保護の概要

多くの場合、web アプリケーションは、セキュリティ上重要なデータを格納する必要があります。 Windows では、デスクトップ アプリケーションの DPAPI が用意されていますが、これは web アプリケーションに適していません。 ASP.NET Core データ保護スタックは、開発者は、キー管理と回転をなど、データの保護を使用して単純で使いやすい暗号化 API を提供します。

ASP.NET Core データ保護スタックの代わりの長期的なとして使用するものでは、 <machineKey> ASP.NET 内の要素 1.x - 4.x です。 大部分の最新のアプリケーションが発生する可能性の高いユース ケースのボックスのソリューションを提供しつつ、古い暗号化スタックの欠点の多くに対処することがあります。

## <a name="problem-statement"></a>問題の説明

1 文では、全体的な問題の説明を簡潔に記述できます。 後で取得は、信頼されている情報を保持する必要がありますが、永続化メカニズムは信用できません。 Web には、この記述できます「必要な信頼されていないクライアントを介してラウンド トリップの信頼された状態にします」

この典型的な例は、認証 cookie、またはベアラー トークン。 サーバーは、生成、「I Groot am し、"xyz"のアクセス許可を持って」トークンし、クライアントに渡します。 今後、クライアントは現時点では、サーバーにそのトークンが、サーバーには、ある種のクライアントがトークンを偽造していないことを保証が必要があります。 したがって、最初の要件: 信頼性 (ルール サブタイプ) 保全性、不正使用防止機能校正)。

永続化された状態がサーバーによって信頼されているために、この状態では、この運用環境に固有の情報を含む可能性があるが予想されます。 これは、ファイルのパス、アクセス許可、ハンドル、またはその他の間接参照のフォームまたはその他の一部のサーバーに固有のデータになっている可能性があります。 このような情報一般に公開すべき信頼されていないクライアントにします。 したがって、2 番目の要件: 機密性です。

最後に、最新のアプリケーションは、コンポーネント ベースから見てきましたは個々 のコンポーネントは、システムの他のコンポーネントに関係なく、このシステムの利用します。 たとえば、ベアラー トークン コンポーネントは、このスタックで使用されている場合は可能性があります、同じスタックを使用するもあるアンチ CSRF 機構から干渉なし動作必要があります。 最後の要件したがって: 分離します。

提供できますさらに制約要件の範囲を絞り込むためにします。 内の暗号方式で動作するすべてのサービスが均等に信頼されていること、およびデータが生成または、直接的な制御サービスの外部で使用する必要がないことを想定しています。 さらに、ため、web サービスへの各要求が、暗号方式を 1 つまたは複数回の操作ができるだけ高速である必要があります。 これにより、対称暗号方式は、このシナリオに最適となどに必要になった時間まで非対称暗号化方式を割引おことができます。

## <a name="design-philosophy"></a>デザインの理念

既存のスタックで問題を識別することによって開始します。 発生しました。 をお既存のソリューションのランドス ケープを調査対象し、する既存のソリューション非常なかったシークお機能という結論に達しました。 いくつかの指針に基づいてソリューションを設計しました。

* システムでは、構成の簡略化のためを提供する必要があります。 理想的には、システム構成不要、開発者が実行されている地上をヒット可能性があります。 開発者が (キー リポジトリ) などの特定の側面を構成する必要がある場合、それらの特定の構成を単純に提供するために考慮対象としてを指定してください。

* 単純な消費者向けの API を提供します。 Api は、適切に使用する簡単で不適切に使用するが困難にする必要があります。

* 開発者は、キー管理の原則を理解する必要があります。 システムには、アルゴリズムの選択と、開発者の代理でキーの有効期間を処理する必要があります。 理想的には、開発者は、生のキー マテリアルへのアクセスもが必要です。

* 可能であれば残りの部分でキーを保護する必要があります。 システムは、適切な既定保護メカニズムを確認し、自動的に適用する必要があります。

これらの原則を念頭に、単純なを開発しました[使いやすい](using-data-protection.md)データ保護スタック。

主に、ASP.NET Core データ保護 Api は社外秘のペイロードの無期限の持続性の目的ではありません。 他のテクノロジと同様に[Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx)と[Azure Rights Management](https://technet.microsoft.com/library/jj585024.aspx)より永続的ストレージのシナリオに適している、およびこれに対応して強力なキー管理機能があります。 ただし、機密データの長期的な保護の ASP.NET Core データ保護 Api を使用して、開発者を禁止することは nothing を使用する必要があります。

## <a name="audience"></a>対象ユーザー

データ保護システムは、5 つの主要なパッケージに分割されます。 これらの Api のさまざまな側面の対象 3 つの主な対象ユーザーです。

1. [コンシューマー Api の概要](consumer-apis/overview.md)アプリケーションおよびフレームワークの開発者を対象します。

   "しないスタックの動作方法に関するまたはそれを構成する方法について説明します。 単純にする Api を正常に使用する可能性が高い、できるだけ単純なとしてのなんらかの操作方法を実行します。

2. [構成 Api](configuration/overview.md)アプリケーションの開発者およびシステム管理者を対象します。

   「に伝える必要データ保護システムの環境には、既定以外のパスまたはの設定が必要である。」

3. 機能拡張 Api ターゲット開発者は、カスタム ポリシーの実装を担当します。 これらの Api の使用状況はまれな状況に制限されますが発生したため、セキュリティの対応する開発者です。

   "を本当に固有の動作要件があるため、システム内でコンポーネント全体を置き換える必要があります。 つもりを自分の要件を満たしているプラグインを構築するために、API サーフェスの uncommonly に使用される部分を参照してください。"

## <a name="package-layout"></a>パッケージのレイアウト

データ保護スタックは、5 つのパッケージで構成されます。

* Microsoft.AspNetCore.DataProtection.Abstractions には、基本的な IDataProtectionProvider および IDataProtector インターフェイスが含まれています。 これらの種類 (IDataProtector.Protect のオーバー ロードなど) で作業を支援できる便利拡張メソッドも含まれています。 詳細については、コンシューマー インターフェイスのセクションを参照してください。 場合は、他のユーザーの責任者、データ保護システムをインスタンス化して、Api を使用しているだけで、参照 Microsoft.AspNetCore.DataProtection.Abstractions にします。

* Microsoft.AspNetCore.DataProtection には、暗号化操作の中核となる、キー管理、構成、および機能拡張を含む、データ保護システムの基本的な実装が含まれています。 データ保護システムをインスタンス化を担当する場合 (たとえばに追加する、IServiceCollection) Microsoft.AspNetCore.DataProtection の参照にする変更、またはその動作を拡張する、または。

* Microsoft.AspNetCore.DataProtection.Extensions には、開発者が役に立つ場合がありますが、コア パッケージに属していませんが、追加の Api が含まれています。 たとえば、このパッケージには、単純な「依存関係の挿入設定のない特定のキー記憶域ディレクトリを指しているシステムをインスタンス化」API (詳細) が含まれています。 保護されたペイロード (詳細) の有効期間を制限するための拡張メソッドも含まれています。

* Microsoft.AspNetCore.DataProtection.SystemWeb をリダイレクトする既存の ASP.NET 4.x アプリケーションにインストールすることができます、<machineKey>代わりに、新しいデータ保護スタックを使用する操作。 参照してください[互換性](compatibility/replacing-machinekey.md#compatibility-replacing-machinekey)詳細についてはします。

* Microsoft.AspNetCore.Cryptography.KeyDerivation は PBKDF2 パスワードのルーチンをハッシュの実装を提供し、ユーザーのパスワードを安全に処理する必要があるシステムで使用できます。 参照してください[パスワード ハッシュ](consumer-apis/password-hashing.md)詳細についてはします。