---
title: "アプリ シークレットは、ASP.NET Core での開発中の安全な格納場所"
author: rick-anderson
description: "開発中に機密情報を安全に格納する方法を示します"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
ms.openlocfilehash: 99a1129549d6b9802315c7e5accfa22907994a41
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2017
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a>アプリ シークレットは、ASP.NET Core での開発時の安全な格納場所

<a name=security-app-secrets></a>

によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、および[Scott Addie](https://scottaddie.com) 

このドキュメントでは、開発でシークレット マネージャー ツールを使用して、コードからシークレットを保持する方法を示しています。 最も重要な点は、ソース コードで、パスワードや他の機密データを格納する必要がありますしないと、開発およびテスト モードで運用環境の機密情報を使用しないでください。 代わりに使用することができます、[構成](../fundamentals/configuration.md)システム環境変数からこれらの値を読み取るまたはシークレット マネージャーを使用して格納されている値からツールです。 シークレット マネージャー ツールでは、機密データがソース管理にチェックインされているを防ぐのに役立ちます。 [構成](../fundamentals/configuration.md)システムは、この記事で説明されているシークレット マネージャー ツールを使用して格納されているシークレットを読み取ることができます。

シークレット マネージャー ツールは、開発でのみ使用されます。 Azure のテストおよび運用シークレットを保護することができます、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーです。 参照してください[Azure Key Vault の構成プロバイダー](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration)詳細についてはします。

## <a name="environment-variables"></a>環境変数

コードまたはローカルの構成ファイルでは、アプリ シークレットを格納するを回避するのには、環境変数に機密情報を保存します。 セットアップすることができます、[構成](../fundamentals/configuration.md)を呼び出して環境変数から値を読み取るために、フレームワーク`AddEnvironmentVariables`です。 環境変数を使用して以前に指定した構成のすべてのソースの構成値を上書きできます。

個々 のユーザー アカウントを持つ新しい ASP.NET Core web アプリを作成する場合に、追加するなど、既定の接続文字列を*される appsettings.json*キーを持つプロジェクト ファイルで`DefaultConnection`です。 既定の接続文字列は、ユーザー モードで実行され、パスワードを必要としない LocalDB を使用するセットアップです。 オーバーライドすることができます、アプリケーションをテストまたは実稼働サーバーを展開するときに、`DefaultConnection`で環境変数の設定をテストまたは実稼働データベースの (場合によっては資格情報の機密)、接続文字列を含むキーの値サーバー。

>[!WARNING]
> 環境変数は、プレーン テキストで格納されると、暗号化されていません。 コンピューターまたはプロセスが侵害された場合、環境変数は信頼されていないパーティによってアクセスできます。 追加の手段をユーザーの機密情報の漏えいを防ぐためには、必要な可能性があります。

## <a name="secret-manager"></a>シークレット マネージャー

シークレット マネージャー ツールは、プロジェクト ツリーの外部で開発作業の機密データを格納します。 シークレット マネージャー ツールは、の機密情報の格納に使用できるプロジェクト ツール、 [.NET Core](https://microsoft.com/net/core)開発中のプロジェクトです。 シークレット マネージャー ツール アプリ シークレットは、特定のプロジェクトに関連付けるでき、それらを複数のプロジェクト間で共有できます。

>[!WARNING]
> シークレット マネージャー ツールは、格納された機密情報は暗号化されず、信頼できるストアとして扱うことはできません。 これは、開発目的でのみです。 キーと値は、ユーザーのプロファイル ディレクトリに JSON 構成ファイルに格納されます。

### <a name="visual-studio-2017-installing-the-secret-manager-tool"></a>Visual Studio 2017: シークレット マネージャー ツールをインストールします。

ソリューション エクスプ ローラーでプロジェクトを右クリックし **編集\<project_name\>.csproj**コンテキスト メニュー。 強調表示された行を追加、 *.csproj*ファイル、および関連付けられている NuGet パッケージを復元する保存します。

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]

ソリューション エクスプ ローラーでプロジェクトを右クリックし **管理ユーザーの機密情報**コンテキスト メニュー。 このジェスチャが新しく追加`UserSecretsId`内のノード、`PropertyGroup`の*.csproj*ファイル。 これを開くことも、`secrets.json`ファイル テキスト エディターでします。

`secrets.json` に次のコードを追加します。

```json
{
    "MySecret": "ValueOfMySecret"
}
```

### <a name="visual-studio-2015-installing-the-secret-manager-tool"></a>Visual Studio 2015: シークレット マネージャー ツールをインストールします。

プロジェクトの`project.json`ファイル。 参照を追加`Microsoft.Extensions.SecretManager.Tools`内で、`tools`プロパティ、および関連付けられている NuGet パッケージを復元する保存。

```json
"tools": {
    "Microsoft.Extensions.SecretManager.Tools": "1.0.0-preview2-final",
    "Microsoft.AspNetCore.Server.IISIntegration.Tools": "1.0.0-preview2-final"
},
```

ソリューション エクスプ ローラーでプロジェクトを右クリックし **管理ユーザーの機密情報**コンテキスト メニュー。 このジェスチャが新しく追加`userSecretsId`プロパティを`project.json`です。 これを開くことも、`secrets.json`ファイル テキスト エディターでします。

`secrets.json` に次のコードを追加します。

```json
{
    "MySecret": "ValueOfMySecret"
}
```

### <a name="visual-studio-code-or-command-line-installing-the-secret-manager-tool"></a>Visual Studio Code またはコマンド ライン: シークレット マネージャー ツールをインストールします。

追加`Microsoft.Extensions.SecretManager.Tools`を*.csproj*ファイル実行して`dotnet restore`です。

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]

次のコマンドを実行して、シークレット マネージャー ツールをテストします。

```console
dotnet user-secrets -h
```

シークレット マネージャー ツールは、使用、オプションおよびコマンドのヘルプに表示されます。

> [!NOTE]
> 同じディレクトリにする必要があります、 *.csproj*で定義されているツールを実行するファイル、 *.csproj*ファイルの`DotNetCliToolReference`ノード。

シークレット マネージャー ツールは、ユーザー プロファイルに格納されているプロジェクトに固有の構成設定で動作します。 ユーザーの機密情報を使用するプロジェクトを指定する必要があります、`UserSecretsId`値でその*.csproj*ファイル。 値`UserSecretsId`任意で、一般にプロジェクトに固有です。 開発者が通常の GUID を生成、`UserSecretsId`です。

追加、`UserSecretsId`に、プロジェクトの*.csproj*ファイル。

[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?range=7-9&highlight=2)]

シークレット マネージャー ツールを使用すると、機密情報を設定します。 たとえば、コマンド ウィンドウで、プロジェクト ディレクトリから、次のように入力します。

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

その他のディレクトリからシークレット マネージャー ツールを実行することができますが、使用する必要があります、`--project`へのパスに渡すオプション、 *.csproj*ファイル。
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

シークレット マネージャー ツールを使用して、一覧を削除し、アプリのシークレットをオフにすることができますも。

## <a name="accessing-user-secrets-via-configuration"></a>構成を使用してユーザーの機密情報にアクセスします。

構成システムを介したシークレット Manager の機密情報にアクセスします。 追加、`Microsoft.Extensions.Configuration.UserSecrets`パッケージ化し、実行`dotnet restore`です。

ユーザーの機密情報の構成ソースを追加、`Startup`メソッド。

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]

構成 API を使用してユーザーの機密情報にアクセスすることができます。

[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]

## <a name="how-the-secret-manager-tool-works"></a>シークレット マネージャー ツールの動作

値が格納されている場所と方法など、実装の詳細を抽象シークレット Manager ツール。 これらの実装の詳細を知ることがなくツールを使用することができます。 現在のバージョンで、値が格納されている、 [JSON](http://json.org/)ユーザーのプロファイル ディレクトリに構成ファイル。

* Windows:`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`

* Linux:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

* Mac:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`

値`userSecretsId`で指定された値に由来*.csproj*ファイル。

これらの実装の詳細を変更する可能性があります、場所やシークレット マネージャー ツールを使用して保存するデータの形式に依存するコードを記述する必要がありますされません。 たとえば、シークレットの値が現在は*いない*今日では、暗号化しますが、いつか可能性があります。

## <a name="additional-resources"></a>その他のリソース

* [構成](../fundamentals/configuration.md)