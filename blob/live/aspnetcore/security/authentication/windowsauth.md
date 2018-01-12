---
title: "ASP.NET Core で Windows 認証を構成します。"
author: ardalis
description: "この記事では、IIS Express、IIS、HTTP.sys および WebListener を使用して、ASP.NET Core で Windows 認証を構成する方法について説明します。"
keywords: "ASP.NET Core、Windows 認証、Authorize attribute、AllowAnonymous 属性"
ms.author: riande
manager: wpickett
ms.date: 10/24/2017
ms.topic: article
ms.assetid: cf119f21-1a2b-49a2-b052-548ccb66ee83
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/windowsauth
ms.openlocfilehash: e5ceffe5b7f7e3ef4f6158b6b7b7d571a21ee130
ms.sourcegitcommit: 12e5194936b7e820efc5505a2d5d4f84e88eb5ef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2018
---
# <a name="configure-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="69b92-104">ASP.NET Core アプリケーションの Windows 認証を構成します。</span><span class="sxs-lookup"><span data-stu-id="69b92-104">Configure Windows authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="69b92-105">によって[Steve Smith](https://ardalis.com)と[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="69b92-105">By [Steve Smith](https://ardalis.com) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="69b92-106">Windows 認証は、IIS でホストされている ASP.NET Core アプリケーションに対して設定できる[HTTP.sys](xref:fundamentals/servers/httpsys)、または[WebListener](xref:fundamentals/servers/weblistener)です。</span><span class="sxs-lookup"><span data-stu-id="69b92-106">Windows authentication can be configured for ASP.NET Core apps hosted with IIS, [HTTP.sys](xref:fundamentals/servers/httpsys), or [WebListener](xref:fundamentals/servers/weblistener).</span></span>

## <a name="what-is-windows-authentication"></a><span data-ttu-id="69b92-107">Windows 認証とは何ですか。</span><span class="sxs-lookup"><span data-stu-id="69b92-107">What is Windows authentication?</span></span>

<span data-ttu-id="69b92-108">Windows 認証は、ASP.NET Core アプリケーションのユーザーを認証するオペレーティング システムに依存します。</span><span class="sxs-lookup"><span data-stu-id="69b92-108">Windows authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="69b92-109">ユーザーを識別する Active Directory ドメインの id またはその他の Windows アカウントを使用して、企業ネットワークで実行されると、サーバーは、Windows 認証を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="69b92-109">You can use Windows authentication when your server runs on a corporate network using Active Directory domain identities or other Windows accounts to identify users.</span></span> <span data-ttu-id="69b92-110">Windows 認証は、同じ Windows ドメインにユーザー、クライアント アプリケーションおよび web サーバーが属している、イントラネット環境に最も適しています。</span><span class="sxs-lookup"><span data-stu-id="69b92-110">Windows authentication is best suited to intranet environments in which users, client applications, and web servers belong to the same Windows domain.</span></span>

<span data-ttu-id="69b92-111">[Windows 認証と IIS のインストールの詳細について](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)です。</span><span class="sxs-lookup"><span data-stu-id="69b92-111">[Learn more about Windows authentication and installing it for IIS](https://docs.microsoft.com/iis/configuration/system.webServer/security/authentication/windowsAuthentication/).</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="69b92-112">ASP.NET Core アプリケーションの Windows 認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="69b92-112">Enable Windows authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="69b92-113">Visual Studio Web アプリケーション テンプレートは、Windows 認証をサポートするために構成されていることができます。</span><span class="sxs-lookup"><span data-stu-id="69b92-113">The Visual Studio Web Application template can be configured to support Windows authentication.</span></span>

### <a name="use-the-windows-authentication-app-template"></a><span data-ttu-id="69b92-114">Windows 認証アプリ テンプレートを使用します。</span><span class="sxs-lookup"><span data-stu-id="69b92-114">Use the Windows authentication app template</span></span>

<span data-ttu-id="69b92-115">Visual Studio で。</span><span class="sxs-lookup"><span data-stu-id="69b92-115">In Visual Studio:</span></span>
1. <span data-ttu-id="69b92-116">新しい ASP.NET Core Web アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="69b92-116">Create a new ASP.NET Core Web Application.</span></span> 
1. <span data-ttu-id="69b92-117">テンプレートの一覧から Web アプリケーションを選択します。</span><span class="sxs-lookup"><span data-stu-id="69b92-117">Select Web Application from the list of templates.</span></span>
1. <span data-ttu-id="69b92-118">選択、**認証の変更**ボタンをクリックして**Windows 認証**です。</span><span class="sxs-lookup"><span data-stu-id="69b92-118">Select the **Change Authentication** button and select **Windows Authentication**.</span></span> 

<span data-ttu-id="69b92-119">アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="69b92-119">Run the app.</span></span> <span data-ttu-id="69b92-120">上部にある ユーザー名が表示されるアプリの右。</span><span class="sxs-lookup"><span data-stu-id="69b92-120">The username appears in the top right of the app.</span></span>

![Windows 認証のブラウザーのスクリーン ショット](windowsauth/_static/browser-screenshot.png)

<span data-ttu-id="69b92-122">IIS Express を使用して開発作業では、テンプレートは、Windows 認証を使用するために必要なすべての構成を提供します。</span><span class="sxs-lookup"><span data-stu-id="69b92-122">For development work using IIS Express, the template provides all the configuration necessary to use Windows authentication.</span></span> <span data-ttu-id="69b92-123">次のセクションでは、Windows 認証用の ASP.NET Core アプリを手動で構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="69b92-123">The following section shows how to manually configure an ASP.NET Core app for Windows authentication.</span></span>

### <a name="visual-studio-settings-for-windows-and-anonymous-authentication"></a><span data-ttu-id="69b92-124">Windows および匿名認証用 visual Studio の設定</span><span class="sxs-lookup"><span data-stu-id="69b92-124">Visual Studio settings for Windows and anonymous authentication</span></span>

<span data-ttu-id="69b92-125">Visual Studio プロジェクト**プロパティ**ページの**デバッグ** タブは、Windows 認証と匿名認証のチェック ボックスを表示します。</span><span class="sxs-lookup"><span data-stu-id="69b92-125">The Visual Studio project **Properties** page's **Debug** tab provides check boxes for Windows authentication and anonymous authentication.</span></span>

![Windows 認証のブラウザーのスクリーン ショット](windowsauth/_static/vs-auth-property-menu.png)

<span data-ttu-id="69b92-127">これら 2 つのプロパティを構成する代わりに、 *launchSettings.json*ファイル。</span><span class="sxs-lookup"><span data-stu-id="69b92-127">Alternatively, these two properties can be configured in the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample/launchSettings.json?highlight=3-4)]

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="69b92-128">IIS での Windows 認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="69b92-128">Enable Windows authentication with IIS</span></span>

<span data-ttu-id="69b92-129">IIS を使用して、 [ASP.NET Core モジュール](xref:fundamentals/servers/aspnet-core-module)ASP.NET Core アプリケーションをホストするには、(ANCM)。</span><span class="sxs-lookup"><span data-stu-id="69b92-129">IIS uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) (ANCM) to host ASP.NET Core apps.</span></span> <span data-ttu-id="69b92-130">ANCM フロー Windows 認証を IIS に既定でします。</span><span class="sxs-lookup"><span data-stu-id="69b92-130">The ANCM flows Windows authentication to IIS by default.</span></span> <span data-ttu-id="69b92-131">Windows 認証の構成については、アプリケーション プロジェクトではなく、IIS 内で実行します。</span><span class="sxs-lookup"><span data-stu-id="69b92-131">Configuration of Windows authentication is done within IIS, not the application project.</span></span> <span data-ttu-id="69b92-132">次のセクションでは、IIS マネージャーを使用して、Windows 認証を使用する ASP.NET Core アプリケーションを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="69b92-132">The following sections show how to use IIS Manager to configure an ASP.NET Core app to use Windows authentication.</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="69b92-133">新しい IIS サイトを作成します。</span><span class="sxs-lookup"><span data-stu-id="69b92-133">Create a new IIS site</span></span>

<span data-ttu-id="69b92-134">名前とフォルダーを指定し、新しいアプリケーション プールを作成することを許可します。</span><span class="sxs-lookup"><span data-stu-id="69b92-134">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="customize-authentication"></a><span data-ttu-id="69b92-135">認証をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="69b92-135">Customize authentication</span></span>

<span data-ttu-id="69b92-136">サイトの [認証] メニューを開きます。</span><span class="sxs-lookup"><span data-stu-id="69b92-136">Open the Authentication menu for the site.</span></span>

![IIS 認証 メニュー](windowsauth/_static/iis-authentication-menu.png)

<span data-ttu-id="69b92-138">匿名認証を無効にして、Windows 認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="69b92-138">Disable Anonymous Authentication and enable Windows Authentication.</span></span>

![IIS の認証設定](windowsauth/_static/iis-auth-settings.png)

### <a name="publish-your-project-to-the-iis-site-folder"></a><span data-ttu-id="69b92-140">IIS サイトのフォルダーにプロジェクトを発行します。</span><span class="sxs-lookup"><span data-stu-id="69b92-140">Publish your project to the IIS site folder</span></span>

<span data-ttu-id="69b92-141">Visual Studio または .NET Core CLI を使用して、インストール先フォルダーをアプリの発行します。</span><span class="sxs-lookup"><span data-stu-id="69b92-141">Using Visual Studio or the .NET Core CLI, publish the app to the destination folder.</span></span>

![Visual Studio 発行のダイアログ ボックス](windowsauth/_static/vs-publish-app.png)

<span data-ttu-id="69b92-143">詳細については[を IIS に発行](xref:host-and-deploy/iis/index)です。</span><span class="sxs-lookup"><span data-stu-id="69b92-143">Learn more about [publishing to IIS](xref:host-and-deploy/iis/index).</span></span>

<span data-ttu-id="69b92-144">Windows 認証が動作していることを確認するアプリを起動します。</span><span class="sxs-lookup"><span data-stu-id="69b92-144">Launch the app to verify Windows authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys-or-weblistener"></a><span data-ttu-id="69b92-145">HTTP.sys や WebListener で Windows 認証を有効にします。</span><span class="sxs-lookup"><span data-stu-id="69b92-145">Enable Windows authentication with HTTP.sys or WebListener</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="69b92-146">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="69b92-146">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="69b92-147">Kestrel が Windows 認証をサポートしていないは使用できます[HTTP.sys](xref:fundamentals/servers/httpsys) Windows では自己ホスト型のシナリオをサポートするためにします。</span><span class="sxs-lookup"><span data-stu-id="69b92-147">Although Kestrel doesn't support Windows authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="69b92-148">次の例は、Windows 認証での HTTP.sys を使用するアプリの web ホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="69b92-148">The following example configures the app's web host to use HTTP.sys with Windows authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program2x.cs?highlight=9-14)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="69b92-149">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="69b92-149">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="69b92-150">Kestrel が Windows 認証をサポートしていないは使用できます[WebListener](xref:fundamentals/servers/weblistener) Windows では自己ホスト型のシナリオをサポートするためにします。</span><span class="sxs-lookup"><span data-stu-id="69b92-150">Although Kestrel doesn't support Windows authentication, you can use [WebListener](xref:fundamentals/servers/weblistener) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="69b92-151">次の例は、Windows 認証で WebListener を使用するアプリの web ホストを構成します。</span><span class="sxs-lookup"><span data-stu-id="69b92-151">The following example configures the app's web host to use WebListener with Windows authentication:</span></span>

[!code-csharp[](windowsauth/sample/Program1x.cs?highlight=6-11)]

---

## <a name="work-with-windows-authentication"></a><span data-ttu-id="69b92-152">Windows 認証を使用します。</span><span class="sxs-lookup"><span data-stu-id="69b92-152">Work with Windows authentication</span></span>

<span data-ttu-id="69b92-153">匿名アクセスの構成の状態を決定する方法、`[Authorize]`と`[AllowAnonymous]`属性は、アプリで使用します。</span><span class="sxs-lookup"><span data-stu-id="69b92-153">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="69b92-154">次の 2 つのセクションでは、匿名アクセスの許可されていないと許可されている構成の状態を処理する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="69b92-154">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="69b92-155">匿名アクセスを許可しません。</span><span class="sxs-lookup"><span data-stu-id="69b92-155">Disallow anonymous access</span></span>

<span data-ttu-id="69b92-156">Windows 認証が有効になっており、匿名アクセスが無効になっているときに、`[Authorize]`と`[AllowAnonymous]`属性が影響を与えるありません。</span><span class="sxs-lookup"><span data-stu-id="69b92-156">When Windows authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="69b92-157">IIS サイト (または HTTP.sys または WebListener サーバー) への匿名アクセスを許可しないように構成する場合、要求がアプリに到達しません。</span><span class="sxs-lookup"><span data-stu-id="69b92-157">If the IIS site (or HTTP.sys or WebListener server) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="69b92-158">このため、`[AllowAnonymous]`属性は適用されません。</span><span class="sxs-lookup"><span data-stu-id="69b92-158">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="69b92-159">匿名アクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="69b92-159">Allow anonymous access</span></span>

<span data-ttu-id="69b92-160">Windows 認証と匿名アクセスの両方が有効になっているときに使用して、`[Authorize]`と`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="69b92-160">When both Windows authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="69b92-161">`[Authorize]`属性では、Windows 認証を必要と本当にアプリの部分をセキュリティで保護することができます。</span><span class="sxs-lookup"><span data-stu-id="69b92-161">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows authentication.</span></span> <span data-ttu-id="69b92-162">`[AllowAnonymous]`属性のオーバーライド`[Authorize]`属性の匿名アクセスを許可するアプリ内で使用します。</span><span class="sxs-lookup"><span data-stu-id="69b92-162">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="69b92-163">参照してください[単純な承認](xref:security/authorization/simple)属性の使用方法の詳細。</span><span class="sxs-lookup"><span data-stu-id="69b92-163">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="69b92-164">ASP.NET Core で 2.x、`[Authorize]`属性で追加の構成が必要です。 *Startup.cs* Windows 認証の匿名要求を身にします。</span><span class="sxs-lookup"><span data-stu-id="69b92-164">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows authentication.</span></span> <span data-ttu-id="69b92-165">推奨される構成によって若干異なります、web サーバーが使用されています。</span><span class="sxs-lookup"><span data-stu-id="69b92-165">The recommended configuration varies slightly based on the web server being used.</span></span>

#### <a name="iis"></a><span data-ttu-id="69b92-166">IIS</span><span class="sxs-lookup"><span data-stu-id="69b92-166">IIS</span></span>

<span data-ttu-id="69b92-167">IIS を使用する場合に、次を追加、`ConfigureServices`メソッド。</span><span class="sxs-lookup"><span data-stu-id="69b92-167">If using IIS, add the following to the `ConfigureServices` method:</span></span> 

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="69b92-168">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="69b92-168">HTTP.sys</span></span>

<span data-ttu-id="69b92-169">HTTP.sys を使用する場合に、次の追加、`ConfigureServices`メソッド。</span><span class="sxs-lookup"><span data-stu-id="69b92-169">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="69b92-170">偽装</span><span class="sxs-lookup"><span data-stu-id="69b92-170">Impersonation</span></span>

<span data-ttu-id="69b92-171">ASP.NET Core は、権限借用を実装していません。</span><span class="sxs-lookup"><span data-stu-id="69b92-171">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="69b92-172">アプリは、アプリケーション プールまたはプロセス id を使用して、すべての要求のアプリケーション id で実行されます。</span><span class="sxs-lookup"><span data-stu-id="69b92-172">Apps run with the application identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="69b92-173">ユーザーの代理としてアクションを明示的に実行する必要がある場合を使用して`WindowsIdentity.RunImpersonated`です。</span><span class="sxs-lookup"><span data-stu-id="69b92-173">If you need to explicitly perform an action on behalf of a user, use `WindowsIdentity.RunImpersonated`.</span></span> <span data-ttu-id="69b92-174">このコンテキストで 1 つのアクションを実行し、コンテキストを閉じます。</span><span class="sxs-lookup"><span data-stu-id="69b92-174">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample/Startup.cs?name=snippet_Impersonate&highlight=10-18)]

<span data-ttu-id="69b92-175">なお`RunImpersonated`非同期操作をサポートしないし、複雑なシナリオで使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="69b92-175">Note that `RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="69b92-176">全体の要求またはミドルウェア チェーンをラッピングされていないはサポートされてなど、ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="69b92-176">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

---