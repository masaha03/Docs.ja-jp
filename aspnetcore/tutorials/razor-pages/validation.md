---
title: ASP.NET Core Razor ページに検証を追加する
author: rick-anderson
description: ASP.NET Core で Razor ページに検証を追加する方法について説明します。
manager: wpickett
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 08/07/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/validation
ms.openlocfilehash: bf3cfd8ce7616807bae4bcacf09b63e54c8fae55
ms.sourcegitcommit: 74be78285ea88772e7dad112f80146b6ed00e53e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>ASP.NET Core Razor ページに検証を追加する

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

ここでは、`Movie` モデルに検証ロジックを追加します。 検証規則は、ユーザーがムービーを作成または編集するときに、いつでも適用できます。

## <a name="validation"></a>検証

ソフトウェア開発には、[DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself" (繰り返しを避けること)) という重要な理念があります。 Razor ページでは、機能を一度規定したら、それをアプリ全体に反映する開発を推奨しています。 DRY によって、アプリのコード量を減らすことができます。 DRY を実施すると、コードのエラーが発生する可能性が低くなり、テストと保守が簡単になります。

Razor ページと Entity Framework が提供している検証のサポートは、DRY 原則の好例です。 検証規則は、1 つの場所 (モデル クラス内) で宣言的に規定され、アプリの任意の場所で適用されます。

### <a name="adding-validation-rules-to-the-movie-model"></a>検証規則をムービー モデルを追加する

*Movie.cs* ファイルを開きます。 [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) には、クラスまたはプロパティに宣言的に適用される組み込みの検証属性セットがあります。 また、DataAnnotations には、検証設定を支援し、検証を行わない `DataType` のような書式設定属性もあります。

`Required`、`StringLength`、`RegularExpression`、および `Range` 検証属性を利用するように `Movie` クラスを更新します。

[!code-csharp[](../../tutorials/first-mvc-app/start-mvc//sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

検証属性で、モデルのプロパティに適用されている動作を指定します。

* `Required` と `MinimumLength` の属性は、プロパティに値が必要なことを示します。 ただし、ユーザーが空白を入力することで null 許容型の妥当性制約を満たすことはできます。 null 非許容の[値の型](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/value-types) (`decimal`、`int`、`float`、`DateTime` など) は本質的に必須ではなく、`Required` 属性を必要としません。
* `RegularExpression` 属性は、ユーザーが入力できる文字を制限します。 上記のコードで、`Genre` と `Rating` は、文字のみを使用する必要があります (空白、数字、特殊文字は使用できません)。
* `Range` 属性は、指定した範囲に値を制限します。
* `StringLength` 属性は文字列の最大長を設定します。必要に応じて、最短長も設定できます。 

ASP.NET Core で検証規則を自動的に適用すると、アプリをより堅牢にすることができます。 モデルに自動検証を適用すると、新しいコードを追加したときに適用を思い出す必要がないので、アプリの保護に役立ちます。

### <a name="validation-error-ui-in-razor-pages"></a>Razor ページの検証エラー UI

アプリを実行し、[Pages/Movies]/(ページ/ムービー/) に移動します。

**[Create New]\(新規作成\)** リンクを選択します。 いくつか無効な値を入力してフォームを設定します。 jQuery クライアント側検証がエラーを検出すると、エラー メッセージが表示されます。

![複数 jQuery クライアント側検証エラーが表示されたムービー ビュー フォーム](validation/_static/val.png)

> [!NOTE]
> `Price` フィールドに小数点またはコンマを入力することはできない場合があります。 小数点にコンマ (",") を使用し、英語 (米国) 以外の日付形式を使用する英語以外のロケールの [jQuery 検証](https://jqueryvalidation.org/)をサポートするには、アプリをグローバル化する手順を行う必要があります。 詳しくは、「[その他の技術情報](#additional-resources)」を参照してください。 ここでは、単に 10 のような整数を入力します。

無効な値を含む各フィールドに、検証エラー メッセージが自動的に表示されることがわかります。 エラーは、(JavaScript と jQuery を使用している) クライアント側とサーバー側 (ユーザーが JavaScript を無効にしている場合) の両方に適用されます。

大きな利点は、[Create]/(作成/) ページまたは [Edit]/(編集/) ページの変更が**必要ない**ことです。 DataAnnotations がモデルに適用されたときに、検証 UI は有効になっています。 このチュートリアルで作成した Razor ページでは、(`Movie` モデル クラスのプロパティで検証属性を使用して) 検証規則が自動的に選択されます。 [Edit]/(編集/) ページを使用して検証をテストします。同じ検証が適用されます。

クライアント側の検証エラーがなくなるまで、フォーム データはサーバーにポストされません。 次のうち 1 つまたは複数の方法で、フォーム データがポストされていないことを確認します。

* `OnPostAsync` メソッドにブレークポイントを設定します。 フォームを送信します (**[Create]/(作成/)** または **[Save]/(保存/)** を選択します)。 ブレークポイントがヒットすることはありません。
* [Fiddler ツール](http://www.telerik.com/fiddler)を使用します。
* ブラウザー開発者向けツールを使用して、ネットワーク トラフィックを監視します。

### <a name="server-side-validation"></a>サーバー側の検証

ブラウザーで JavaScript が無効にされている場合、エラーがあるフォームを送信すると、サーバーにポストされます。

必要に応じて、サーバー側の検証をテストします。

* ブラウザーで JavaScript を無効にします。 ブラウザーで JavaScript を無効にすることができない場合は、別のブラウザーを試してください。
* [Create] または [Edit] ページの `OnPostAsync` メソッドにブレークポイントを設定します。
* 検証エラーがあるフォームを送信します。
* モデルの状態が無効であることを確認します。

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

次のコードは、以前のチュートリアルでスキャフォールディング処理した *Create.cshtml* ページの一部です。 [Create] または [Edit] ページで、最初のフォームを表示し、エラーのイベント時にフォームを再表示するために使用されます。

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[入力タグ ヘルパー](xref:mvc/views/working-with-forms)は [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性を使用し、クライアント側で jQuery 検証に必要な HTML 属性を生成します。 [検証タグ ヘルパー](xref:mvc/views/working-with-forms#the-validation-tag-helpers)には検証エラーが表示されます。 詳しくは、[検証に関する記事](xref:mvc/models/validation)をご覧ください。

[Create] ページと [Edit] ページ内に検証規則はありません。 検証規則とエラー文字列は、`Movie` クラスでのみ指定されています。 これらの検証規則は、`Movie` モデルを編集する Razor ページに自動的に適用されます。

検証ロジックの変更が必要な場合は、モデルでのみ変更します。 検証は常にアプリケーション全体に適用されます (検証ロジックは 1 か所で定義されます)。 検証が 1 か所なので、コードが整理された状態を保ち、保守と更新が簡単になります。

## <a name="using-datatype-attributes"></a>DataType 属性の使用

`Movie` クラスを調べます。 `System.ComponentModel.DataAnnotations` 名前空間には、組み込みの検証属性セットに加え、書式設定の属性もあります。 `DataType` 属性は、`ReleaseDate` および `Price` プロパティに適用されます。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` 属性は、ビュー エンジンに対して、データの書式設定のヒントのみを提供します (また、URL の場合に `<a>`、電子メールの場合に `<a href="mailto:EmailAddress.com">` などの属性を提供します)。 `RegularExpression` 属性は、データの書式を検証するために使用されます。 `DataType` 属性は、データベースの組み込み型よりも具体的なデータ型を指定するために使用されます。 `DataType` 属性は、検証属性ではありません。 サンプル アプリケーションでは、日付のみが表示され、時刻は表示されません。

`DataType` 列挙型は、Date、Time、PhoneNumber、Currency、EmailAddress など、多くの型のために用意されています。 また、`DataType` 属性を使用して、アプリケーションで型固有の機能を自動的に提供することもできます。 たとえば、`DataType.EmailAddress` に対して `mailto:` リンクを作成できます。 HTML 5 をサポートしているブラウザーでは、`DataType.Date` に日付セレクターを提供できます。 `DataType` 属性は、HTML 5 ブラウザーが使用する HTML 5 `data-` ("データ ダッシュ" と読みます) 属性を出力します。 `DataType` 属性は、検証を**提供していません**。

`DataType.Date` は、表示される日付の書式を指定しません。 既定で、日付フィールドはサーバーの `CultureInfo` に基づき、既定の書式に従って表示されます。

`DisplayFormat` 属性は、日付の書式を明示的に指定するために使用されます。

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` 設定では、編集対象の値を表示するときに適用する必要がある書式設定を指定します。 一部のフィールドでは、この動作が不要なことがあります。 たとえば、通貨値の場合、編集 UI で通貨記号が不要なことがあります。

`DisplayFormat` 属性を単独で使用できますが、一般的に、`DataType` 属性を使用することが推奨されます。 `DataType` 属性は、画面でのレンダリング方法とは異なり、データのセマンティクスを伝達します。また、DisplayFormat にはない、次の利点があります。

* ブラウザーは HTML5 機能を有効にすることができます (たとえば、カレンダー コントロール、ロケールに適した通貨記号、電子メール リンクを表示するときなど)。
* ブラウザーの既定では、ロケールに基づいて正しい書式を使ってデータがレンダリングされます。
* `DataType` 属性を使用すると、ASP.NET Core フレームワークで、適切なフィールド テンプレートを選択し、データをレンダリングすることができます。 `DisplayFormat` を単独で使用する場合、文字列テンプレートが使用されます。

注: jQuery の検証は、`Range` 属性と `DateTime` では機能しません。 たとえば、次のコードでは、指定した範囲内の日付であっても、クライアント側の検証エラーが常に表示されます。

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

一般的に、モデルに日付をハードコーディングしてコンパイルすることは推奨されません。そのため、`Range` 属性と `DateTime` の使用は推奨されません。

次のコードは、1 行で複数の属性を組み合わせる例です。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

[Razor ページと EF Core の概要](xref:data/ef-rp/intro)に関するページでは、Razor ページでの EF Core 操作についてより詳しく説明されています。

### <a name="publish-to-azure"></a>Azure に発行する

このアプリを Azure に発行する方法については、「[Visual Studio を使用して Azure App Service に ASP.NET Core アプリを発行する](xref:tutorials/publish-to-azure-webapp-using-vs)」をご覧ください。

## <a name="additional-resources"></a>その他の技術情報

* [フォームの操作](xref:mvc/views/working-with-forms)
* [グローバライズとローカライズ](xref:fundamentals/localization)
* [Tag Helpers の概要](xref:mvc/views/tag-helpers/intro)
* [タグ ヘルパーの作成](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [前: 新しいフィールドの追加](xref:tutorials/razor-pages/new-field)
> [次: ファイルのアップロード](xref:tutorials/razor-pages/uploading-files)
