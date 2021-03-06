---
uid: web-forms/overview/moving-to-aspnet-20/server-controls
title: サーバー コントロール |Microsoft ドキュメント
author: microsoft
description: ASP.NET 2.0 では、さまざまな方法でサーバー コントロールを強化します。 このモジュールで、ASP.NET 2.0 の方法と Visual Studio 200 にアーキテクチャの変更の一部について説明します.
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 43f6ac47-76fc-4cf7-8e9f-c18ce673dfd8
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/server-controls
msc.type: authoredcontent
ms.openlocfilehash: 72e9cac7cf9a01791c30783fa56ad7ea205a5a11
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2018
ms.locfileid: "28885194"
---
<a name="server-controls"></a>サーバー コントロール
====================
によって[Microsoft](https://github.com/microsoft)

> ASP.NET 2.0 では、さまざまな方法でサーバー コントロールを強化します。 このモジュールで ASP.NET 2.0 にアーキテクチャの変更の一部を説明し、Visual Studio 2005 は、サーバー コントロールを処理します。


ASP.NET 2.0 では、さまざまな方法でサーバー コントロールを強化します。 このモジュールで ASP.NET 2.0 にアーキテクチャの変更の一部を説明し、Visual Studio 2005 は、サーバー コントロールを処理します。

## <a name="view-state"></a>ビューの状態

ビュー ステートに ASP.NET 2.0 の主な変更は、サイズが大幅に短縮します。 カレンダー コントロールに含まれているページを検討してください。 ASP.NET 1.1 でのビュー ステートを次に示します。

[!code-css[Main](server-controls/samples/sample1.css)]

今すぐここではビュー ステートと同じページで ASP.NET 2.0 のです。

[!code-css[Main](server-controls/samples/sample2.css)]

非常に大きな変更は、ビュー ステートが、ネットワーク上で前後持ち込まれることを検討してこの変更の開発者がパフォーマンスが大幅に増加います。 ビュー ステートのサイズを縮小では、主に内部的に処理したことによるものです。 ビューの状態が、Base64 でエンコードされた文字列ことに注意してください。 ASP.NET 2.0 のビューステートの変更をより深く理解するをみましょう見て、上記の例からデコードされた値。

デコードされた 1.1 ビュー ステートを次に示します。

[!code-css[Main](server-controls/samples/sample3.css)]

これは可能性があります、不自然に少し似た外観がここでパターンがあります。 ASP.NET で 1.x では、単一の文字データ型を識別するために使用し、使用して値を区切り、、 &lt; &gt;文字です。 上記のビュー状態のサンプルでは、"t"は、3 つを表します。 トリプレットには ("l"は、ArrayList を表します)。 ArrayLists のペアが含まれていますInt32 にはそれら ArrayLists のいずれかが含まれています ("i") を 1 およびその他の値を持つ別の組が含まれています。 トリプレットには、ArrayLists などのペアが含まれています。重要な点に注意してくださいがお Triplets のペアを含むはアルファベットを使用してデータ型を割り出すを使用して、&lt;と&gt;区切り記号として文字です。

ASP.NET 2.0 では、デコードされたビュー ステートは少し異なるは検索します。

[!code-powershell[Main](server-controls/samples/sample4.ps1)]

デコードされたビュー ステートの外観に大きな変更を確認する必要があります。 この変更は、いくつかのアーキテクチャの基盤がします。 ビューステートを ASP.NET で 1.x がデータをシリアル化、LosFormatter を使用します。 2.0 では、新しい ObjectStateFormatter クラスを使用します。 このクラスは、ビュー状態、およびコントロールの状態の逆シリアル化とシリアル化に役立つ具体的には設計されています。 (コントロールの状態は次のセクションでカバーされます)。シリアル化と逆シリアル化が行わメソッドを変更することによって得られる利点もあります。 最も大幅なパフォーマンスの 1 つは、TextWriter を使用する LosFormatter とは異なり、ObjectStateFormatter を使用する、BinaryWriter ことです。 これにより、ASP.NET 2.0 をビューステート一連の文字列ではなくバイトを格納できます。 たとえば、整数を取得します。 ASP.NET 1.1 では、整数は、ビュー ステートの 4 バイト必要です。 その同じ整数には、ASP.NET 2.0 では、1 バイトだけが必要です。 格納されているビュー ステートの量を減らす他の機能強化が行われました。 DateTime 値などの保存され、TickCount を使用して、文字列の代わりにします。

場合と、すべてが言えば、特別な注意支払いが行われたという事実 1.x での表示状態の最大のコンシューマーのいずれかのようなコントロールと DataGrid がされていることにします。 ビュー ステートがに関しては、データ グリッドなどのコントロールの主な欠点は、大量の情報が繰り返される頻度が含まれているです。 ASP.NET で 1.x では、および結果として再度肥大化表示状態を介してに単に情報が格納されている繰り返し表示します。 ASP.NET 2.0 では、このようなデータを格納するのに新しい IndexedString クラスを使用します。 文字列を繰り返す場合だけ、IndexedString と IndexedString オブジェクトの実行中のテーブル内のインデックスのトークンを格納します。

## <a name="control-state"></a>コントロールの状態

HTTP ペイロードにして追加したサイズでは開発者のビュー ステートにある主要な gripes のいずれか。 既に触れましたが、DataGrid コントロールのビュー状態の最大のコンシューマーの 1 つです。 大量のデータ グリッドによって生成されたビュー ステートを避けるためには、多くの開発者には、そのコントロールのビュー ステート単に無効になります。 残念ながら、そのソリューションはありませんでした常に正常であります。 ビュー ステート 1.x には ASP.NET では、コントロールの適切な機能に必要なデータだけでなくが含まれています。 コントロールの UI の状態に関する情報も含まれています。 つまり、DataGrid で改ページを表示する UI 情報がすべて必要としない場合でも、ビュー ステートを有効にする必要がありますを許可する場合の状態が含まれています。 これは、0/1 シナリオです。

ASP.NET 2.0 では、コントロールの状態は、コントロールの状態の概要を使用して適切に問題を解決します。 コントロールの状態には、コントロールの適切な機能を絶対に必要なデータが含まれています。 ビュー状態とは異なりコントロールの状態を無効にすることはできません。 したがって、コントロールの状態に格納されているデータを慎重に管理することがあります。

> [!NOTE]
> 表示状態と共にコントロールの状態が永続化、 \_ \_VIEWSTATE 隠しフォーム フィールドです。


このビデオは、ビュー状態、およびコントロールの状態のチュートリアルです。


![](server-controls/_static/image1.png)


[開いているビデオを全画面](server-controls/_static/state1.wmv)


サーバー コントロール状態を制御する読み書きするためには、3 つの手順を行う必要があります。

## <a name="step-1-call-the-registerrequirescontrolstate-method"></a>手順 1: RegisterRequiresControlState メソッドの呼び出し

RegisterRequiresControlState メソッドは、コントロールがコントロールの状態を永続化する必要があることを ASP.NET に通知します。 登録するコントロールにあるコントロールの種類の 1 つの引数を受け取る。

登録では要求からは保持されないことに注意してくださいに重要です。 そのため、このメソッドはコントロールがコントロールの状態を永続化する場合、要求ごとに呼び出す必要があります。 OnInit で、メソッドを呼び出すことをお勧めします。

[!code-csharp[Main](server-controls/samples/sample5.cs)]

## <a name="step-2-override-savecontrolstate"></a>手順 2: SaveControlState をオーバーライドします。

SaveControlState メソッドでは、最後のポスト バック以降コントロール、コントロールの状態の変更を保存します。 コントロールの状態を表すオブジェクトを返します。

## <a name="step-3-override-loadcontrolstate"></a>手順 3: LoadControlState をオーバーライドします。

LoadControlState メソッドは、コントロールに保存された状態を読み込みます。 メソッドは、コントロールの保存された状態を保持するオブジェクトの種類の 1 つの引数を受け取ります。

## <a name="full-xhtml-compliance"></a>完全 XHTML コンプライアンス

Web 開発者は、Web アプリケーションで標準の重要性を認識します。 標準ベースの開発環境を維持するためには、ASP.NET 2.0 と、XHTML 準拠では完全です。 したがって、すべてのタグは HTML 4.0 をサポートするブラウザーで XHTML 標準に従って表示されるか、またはです。

ASP.NET 1.1 DOCTYPE の定義は次のとおりでした。

[!code-html[Main](server-controls/samples/sample6.html)]

ASP.NET 2.0 の既定の DOCTYPE 定義のとおりです。

[!code-html[Main](server-controls/samples/sample7.html)]

選択した場合、構成ファイルで、長期ノードを介して既定 XHML コンプライアンスを変更できます。 たとえば、web.config ファイルに次のノードは XHTML 1.0 Strict を XHTML のコンプライアンス対応を変更します。

[!code-xml[Main](server-controls/samples/sample8.xml)]

選択した場合、構成することも、ASP.NET で使用するレガシの構成を使用する ASP.NET 1.x 次のようにします。

[!code-xml[Main](server-controls/samples/sample9.xml)]

## <a name="adaptive-rendering-using-adapters"></a>アダプティブ アダプターを使用して表示します。

ASP.NET で 1.x では、構成ファイルが含まれている、 &lt;browserCaps&gt; HttpBrowserCapabilities オブジェクトを作成するセクション。 このオブジェクトは、開発者はどのようなデバイスの特定の要求は作成を特定し、コードを適切に表示を許可します。 ASP.NET 2.0 では、モデルが改善され、新しいある ControlAdapter クラスを使用するようになりました。 ある ControlAdapter クラスは、コントロールのライフ サイクルのイベントを上書きし、ユーザー エージェントの能力に基づいてコントロールのレンダリングを制御します。 特定のユーザー エージェントの機能は、c:\windows\microsoft.net\framework\v2.0 に格納されているブラウザー定義ファイル (.browser ファイル拡張子を持つファイル) によって定義されます。\* \* \* \*\CONFIG\Browsers フォルダーです。

> [!NOTE]
> ある ControlAdapter クラスは、抽象クラスです。


ほぼ同様に、 &lt;browserCaps&gt; 1.x では、ブラウザー定義ファイルのセクションでは、正規表現を使用して、要求側のブラウザーを識別するためにユーザー エージェント文字列を解析します。 これは、ユーザー エージェントに対して特定の機能を定義しています。 Render メソッドを使用してコントロールをある ControlAdapter にレンダリングします。 したがって、Render メソッドをオーバーライドする場合呼び出す必要はありませんレンダリング、基本クラスです。 これは、アダプターの 1 回に 1 回と、コントロール自体を 2 回発生するレンダリングにあります。

## <a name="developing-a-custom-adapter"></a>カスタム アダプターの開発

ある ControlAdapter から継承することで、独自のカスタム アダプターを開発できます。 さらに、アダプターが、ページのために必要な場所の場合は、抽象クラス PageAdapter から継承することができます。 使用して、カスタム アダプターへのコントロールのマッピングを実現、 &lt;controlAdapters&gt;ブラウザー定義ファイル内の要素。 たとえば、ブラウザーの定義ファイルから次の XML は、メニュー コントロールを MenuAdapter クラスにマッピングされます。

[!code-html[Main](server-controls/samples/sample10.html)]

このモデルを使用して、コントロールの開発者が特定のデバイスまたはブラウザーを対象とする非常に簡単です。 開発者がすべてのデバイス上のページの表示方法を完全に制御するために非常にシンプルです。

## <a name="per-device-rendering"></a>デバイスごとのレンダリング

ASP.NET 2.0 のサーバー コントロールのプロパティは、指定したデバイスごとのブラウザーに固有のプレフィックスを使用できます。 たとえば、次のコードには、によっては、ページを参照するどのデバイスが使用されるラベルのテキストが変わります。

[!code-aspx[Main](server-controls/samples/sample11.aspx)]

ラベルが「を参照している Internet Explorer からです」というメッセージがテキストを表示するこのラベルを含むページが Internet Explorer から参照されると、 Firefox からページが参照されると、ラベルのテキストを表示」を参照している Firefox からです" その他の任意のデバイスからページが参照されると、「を参照している不明なデバイスからです」が表示されます。 この特別な構文を使用して、任意のプロパティを指定できます。

## <a name="setting-focus"></a>フォーカスを設定

ASP.NET 1.x 開発者は、特定のコントロールに初期フォーカスを設定する方法についてよく寄せられます。 たとえば、ログイン ページで、ユーザー ID ボックスに、ページが初めて読み込まれるときに、フォーカスを取得すると便利ですが。 ASP.NET で 1.x では、これを行うために必要ないくつかのクライアント側スクリプトを記述します。 このスクリプトは、簡単なタスクが、必要がある不要になった ASP.NET 2.0 の SetFocus メソッドご協力に感謝します。 SetFocus メソッドは、フォーカスを受け取る必要のあるコントロールを示す 1 つの引数を受け取ります。 この引数は文字列としてのコントロールのクライアント ID またはコントロール オブジェクトとして、サーバー コントロールの名前にするか、できます。 たとえば、txtUserID というページが初めて読み込まれるときにテキスト ボックス コントロールを初期フォーカスを設定するに ページに次のコードを追加\_負荷。

[!code-csharp[Main](server-controls/samples/sample12.cs)]

--または

[!code-csharp[Main](server-controls/samples/sample13.cs)]

ASP.NET 2.0 では、(前述) Webresource.axd ハンドラーを使用して、フォーカスを設定するクライアント側の関数を表示します。 クライアント側の関数の名前は WebForm\_AutoFocus 次に示すようにします。

[!code-html[Main](server-controls/samples/sample14.html)]

代わりに、コントロールにフォーカス メソッドを使用すると、そのコントロールに初期フォーカスを設定します。 フォーカス メソッドは、コントロール クラスから派生しはすべての ASP.NET 2.0 コントロールに使用できます。 検証エラーが発生したときに、特定のコントロールにフォーカスを設定することもできます。 後のモジュールにとり上げますです。

## <a name="new-server-controls-in-aspnet-20"></a>ASP.NET 2.0 の新しいサーバー コントロール

ASP.NET 2.0 の新しいサーバー コントロールを次に示します。 以降のモジュール内のいくつか詳細に進むされます。

## <a name="imagemap-control"></a>ImageMap コントロール

ImageMap コントロールでは、ホット スポットをポスト バックを開始するか、URL に移動することができますをイメージに追加することができます。 使用可能なホット スポットの 3 つの種類があります。CircleHotSpot、RectangleHotSpot、および PolygonHotSpot です。 ホット スポットは、Visual Studio またはプログラムによってコードでは、コレクション エディターを使用して追加されます。 イメージのホット スポットを描画するために使用できるユーザー インターフェイスはありません。 座標とサイズまたはホット スポットの半径は、宣言によって指定する必要があります。 また、デザイナーでホット スポットの視覚的表現はありません。 ホット スポットが構成した場合、URL に移動して、ホット スポットの NavigateUrl プロパティを使用して、URL が指定されています。 Post の場合は、プロパティを使用すると、サーバー側コードで取得できる、ポストバックで文字列を渡す PostBackValue のホット スポットをバックアップします。


![Visual Studio で hotSpot コレクション エディター](server-controls/_static/image1.jpg)

**図 1**: Visual Studio で HotSpot コレクション エディター


## <a name="bulletedlist-control"></a>BulletedList コントロール

BulletedList コントロールは、データ バインドを簡単にすることができますを箇条書き一覧を示します。 一覧順序を指定できます (番号付き) または BulletStyle プロパティ経由で順序付けられていません。 リスト内の各項目は ListItem オブジェクトによって表されます。


![Visual Studio での BulletedList コントロール](server-controls/_static/image1.gif)

**図 2**: Visual Studio での BulletedList コントロール


## <a name="hiddenfield-control"></a>HiddenField コントロール

HiddenField コントロールは、ページに、値はサーバー側コードで使用可能な非表示のフォーム フィールドを追加します。 隠しフォーム フィールドの値は、通常のバックアップ作成する post 間変更されないままにしてが必要です。 ただし、悪意のあるユーザーをポスト バックする前に、値を変更することはできます。 この場合、HiddenField コントロールに ValueChanged イベントが発生します。 HiddenField コントロール内の機密情報があるが変更されないことを保証する場合は、コードで ValueChanged イベントを処理する必要があります。

## <a name="fileupload-control"></a>ファイルアップロード コントロール

ASP.NET 2.0 のファイルアップロード コントロールにより ASP.NET ページを使用して Web サーバーにファイルをアップロードできます。 このコントロールは ASP.NET 1.x HtmlInputFile クラスは、いくつかの例外を非常に似ています。 ASP.NET で 1.x では、推奨されていた適切なファイルがあったかどうかを判断するために null を PostedFile プロパティをチェックします。 ASP.NET 2.0 のファイルアップロード コントロールは、同じ目的で使用することができます、少し方が効率的に、新しい HasFile プロパティを追加します。

PostedFile プロパティは、HttpPostedFile オブジェクトへのアクセスを引き続き使用できますが、HttpPostedFile の機能の一部がファイルアップロード コントロールの本質的に使用できるようになりました。 たとえば、ASP.NET でアップロードされたファイルを保存する 1.x では、メソッドを呼び出す SaveAs HttpPostedFile オブジェクト。 ファイルアップロード コントロールを使用して、ASP.NET 2.0 では、ファイルアップロード コントロール自体に SaveAs メソッドを呼び出すとします。

2.0 の動作 (およびの場合、最も重要な変更)、もう 1 つの重要な変更は、保存する前にアップロードされたファイル全体をメモリに読み込む必要が不要になったことです。 1.x では、書き込まれる前にメモリに完全にアップロードされた任意のファイルを保存をディスクにします。 このアーキテクチャでは、大きなファイルのアップロードできなくなります。

ASP.NET 2.0 では、requestLengthDiskThreshold 属性 httpRuntime 要素のでは、キロバイト数を構成する書き込まれる前にメモリ内のバッファー内に保持されてディスクにします。

**重要な**: この値がバイト (キロバイト単位ではない) である、既定値は 256 を MSDN ドキュメント (と他の場所のドキュメント) を指定します。 キロバイト単位で指定された値が実際には、既定値は 80 です。 で、既定値は 80 K、いるバッファーが終わらない大きなオブジェクト ヒープのことを確認します。

## <a name="wizard-control"></a>ウィザード コントロール

「ページ」パネルを使用するのか、ページ間を転送することにより、系列内の情報を収集する際に苦慮 ASP.NET 開発者が発生することがよくなります。 ほとんどの場合、努力と手間が 1 つ、時間がかかります。 新しいウィザード コントロールは、線形と非線形の手順に慣れているユーザー ウィザードのインターフェイスにより、問題を解決します。 ウィザード コントロールは、一連の手順で入力フォームを表示します。 各ステップでは、特定の種類のコントロールの StepType プロパティで指定します。 使用可能なステップの種類は次のとおりです。

| **ステップの種類** | **説明** |
| --- | --- |
| 自動 | ウィザードでは、手順階層内の位置に基づいてステップの種類が自動的に決定します。 |
| [開始] | 最初の手順、多くの場合、最初のステートメントを提示するために使用します。 |
| 手順 | 通常の手順です。 |
| 完了 | 最後の手順、通常、ウィザードを完了するためのボタンを表示するために使用します。 |
| 完了 | 成功または失敗を通信してメッセージを表示します。 |

> [!NOTE]
> ウィザード コントロールの追跡の状態を ASP.NET コントロール状態を使用します。 そのため、エントリのプロパティは、任意に不利益をもたらすことがなく false に設定できます。


このビデオは、ウィザード コントロールのチュートリアルです。


![](server-controls/_static/image2.png)


[開いているビデオを全画面](server-controls/_static/wizard1.wmv)


## <a name="localize-control"></a>コントロールをローカライズします。

Localize コントロールは、Literal コントロールに似ています。 ただし、Localize コントロールには、**モード**に追加されるマークアップを表示する方法を制御するプロパティです。 Mode プロパティには、次の値がサポートされています。

| **モード** | **説明** |
| --- | --- |
| 変換 | マークアップは、要求を行っているブラウザーのプロトコルに従って変換されます。 |
| PassThrough | マークアップとして表示されます-はします。 |
| エンコード | コントロールに追加されるマークアップは、HtmlEncode を使用してエンコードされます。 |

## <a name="multiview-and-view-controls"></a>MultiView とビュー コントロール

マルチビュー コントロールのビュー コントロールのコンテナーとして機能し、ビュー コントロールが (と同様にパネル コントロール) の他のコントロールのコンテナーとして機能します。 MultiView コントロール内の各ビューは、1 つのビュー コントロールで表されます。 MultiView の最初のビュー コントロールがビュー 0、2 番目はビュー 1, などです。マルチビュー コントロールの ActiveViewIndex を指定することによって、ビューを切り替えることができます。

## <a name="substitution-control"></a>Substitution コントロール

Substitution コントロールは ASP.NET のキャッシュと組み合わせて使用されます。 ここでキャッシュを活用するためにしたいが、各要求 (つまり、部分ページのキャッシュから除外されている) に更新する必要があるページの部分がある場合は、代入コンポーネントは、優れたソリューションを提供します。 コントロールは、自身で出力を実際にレンダリングしません。 代わりに、サーバー側コード内のメソッドにバインドされます。 ページが要求されたときに、メソッドが呼び出され、返されたマークアップは substitution コントロールの代わりに表示されます。

代替コントロールがバインドされているメソッドを指定した、 **MethodName**プロパティです。 そのメソッドは、次の条件を満たしている必要があります。

- 静的 (VB のでは shared) メソッドがあります。
- これは、型 HttpContext の 1 つのパラメーターを受け取ります。
- ページ上のコントロールを置き換える必要があるマークアップを表す文字列を返します。

Substitution コントロール ページで、他のコントロールを変更する権限がありませんが、パラメーターを使用して現在の HttpContext へのアクセス。

## <a name="gridview-control"></a>GridView コントロール

GridView コントロールは、DataGrid コントロールに代わるです。 このコントロールは、後のモジュールで詳しく説明されます。

## <a name="detailsview-control"></a>DetailsView コントロール

DetailsView コントロールでは、データ ソースから 1 つのレコードを表示および編集または削除することができます。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="formview-control"></a>FormView コントロール

フォーム ビュー コントロールを使用して、構成可能なインターフェイスで、データ ソースから 1 つのレコードを表示します。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="accessdatasource-control"></a>AccessDataSource コントロール

AccessDataSource コントロールが使用するデータは、Access データベースをバインドします。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="objectdatasource-control"></a>ObjectDataSource コントロール

ObjectDataSource コントロールは、コントロールでくデータ バインドされた 2 階層のモデルではなく、中間層のビジネス オブジェクトにコントロールがデータ ソースに直接バインドされてように 3 層アーキテクチャをサポートするために使用されます。 後のモジュールでさらに詳しく説明されます。

## <a name="xmldatasource-control"></a>XmlDataSource コントロール

XmlDataSource コントロールは、XML データ ソースへのデータ バインドに使用されます。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="sitemapdatasource-control"></a>SiteMapDataSource コントロール

SiteMapDataSource コントロールは、サイト マップに基づくサイトのナビゲーション コントロールのデータ バインドを提供します。 後のモジュールでさらに詳しく説明されます。

## <a name="sitemappath-control"></a>SiteMapPath Control

サイト マップ パス コントロールには、一般に呼ば階層リンク ナビゲーション リンクの系列が表示されます。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="menu-control"></a>メニュー コントロール

メニュー コントロールは、DHTML を使用して動的メニューを表示します。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="treeview-control"></a>TreeView コントロール

TreeView コントロールを使用して、データの階層ツリー ビューを表示します。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="login-control"></a>ログイン コントロール

ログイン コントロールは、Web サイトにログインするためのメカニズムを提供します。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="loginview-control"></a>LoginView コントロール

LoginView コントロールは、ユーザーのログイン状態に基づいて異なるテンプレートの表示にできます。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="passwordrecovery-control"></a>PasswordRecovery コントロール

PasswordRecovery コントロールを ASP.NET アプリケーションのユーザーがパスワードを忘れた場合を取得するされます。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="loginstatus"></a>LoginStatus

ログイン状態コントロールは、ユーザーのログイン状態を表示します。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="loginname"></a>LoginName

LoginName コントロールは、ASP.NET アプリケーションにログインしている後に、ユーザーのユーザー名を表示します。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="createuserwizard"></a>CreateUserWizard

CreateUserWizard は、構成可能なウィザードによってユーザーは、ASP.NET アプリケーションで使用する ASP.NET メンバーシップ アカウントを作成する権限です。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="changepassword"></a>ChangePassword

ChangePassword コントロールでは、ASP.NET アプリケーションのパスワードを変更することができます。 これは、後のモジュールでさらに詳しくについて説明します。

## <a name="various-webparts"></a>さまざまな web パーツ

ASP.NET 2.0 では、さまざまな Web パーツに付属します。 これらについては、後のモジュールで詳しく説明します。
