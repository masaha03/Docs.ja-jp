---
uid: web-forms/overview/ajax-control-toolkit/collapsiblepanel/collapsing-and-expanding-a-panel-from-javascript-cs
title: 折りたたみと展開の JavaScript (c#) からパネル |Microsoft ドキュメント
author: wenz
description: ASP.NET AJAX コントロールのツールキットで CollapsiblePanel コントロール パネルを拡張し、その内容を折りたたんだり、展開する機能を提供する.
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: de5500be-75e5-461a-8064-b70ae52ea6a4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/collapsiblepanel/collapsing-and-expanding-a-panel-from-javascript-cs
msc.type: authoredcontent
ms.openlocfilehash: 7baa3be7144946bde7d11afd9b1cb5f14ad9dede
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
ms.locfileid: "30875540"
---
<a name="collapsing-and-expanding-a-panel-from-javascript-c"></a>折りたたみモードと JavaScript (c#) からのパネルを展開します。
====================
によって[Christian Wenz](https://github.com/wenz)

[コードをダウンロードする](http://download.microsoft.com/download/8/a/a/8aab3c3e-de6f-463f-805c-5fda567eef6e/CollapsiblePanel1.cs.zip)または[PDF のダウンロード](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/collapsiblepanel1CS.pdf)

> ASP.NET AJAX コントロールのツールキットで CollapsiblePanel コントロールは、パネルを拡張し、その内容を折りたたむし、再度展開する機能を提供します。 これら 2 つのアクションは、カスタム JavaScript コードからトリガーすることもできます。


## <a name="overview"></a>概要

ASP.NET AJAX コントロールのツールキットで CollapsiblePanel コントロールは、パネルを拡張し、その内容を折りたたむし、再度展開する機能を提供します。 これら 2 つのアクションは、カスタム JavaScript コードからトリガーすることもできます。

## <a name="steps"></a>手順

まず、新しい ASP.NET ページを作成し、含める、`ScriptManager`内、`<form>`要素。 これは、コントロールのツールキットに必要な ASP.NET AJAX ライブラリをロードします。

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample1.aspx)]

次に、展開/折りたたみ効果がわかるようにいくつかのテキストで、パネルを作成します。

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample2.aspx)]

わかりますパネルは、ここで示すようになり、基本的には、背景色とパネルの幅を定義します)、CSS クラスを参照します。

[!code-css[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample3.css)]

`CollapsiblePanelExtender`コントロールに必要な`TargetControlID`属性のツールキットが認識するパネルを折りたたむか、要求時に展開できるようにします。

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample4.aspx)]

残念ながら、エクステンダー現在公開しない特定の API、パネルを展開または折りたたみが、一部の文書化されていないメソッドは操作を行います。 まずを折りたたんだり展開したり、パネルの内容を展開するには、クライアント側 JavaScript をトリガーし、ページに、3 つの HTML ボタンを追加します。

[!code-aspx[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample5.aspx)]

クライアント側の JavaScript コードで (の使用を開始`<script type="text/javascript">`) では、`$find()`メソッドを使用する必要があるアクセスに、`CollapsiblePanelExtender`です。 `$find("cpe")` 参照を返します。 上から、特定のメソッドは、前のタスクを解決します。

メソッド、パネルが呼び出されます (展開) を開くため`_doOpen()`; 次のコードを実装、`doOpen()`関数は、最初のボタンがクリックされたときに呼び出されます。

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample6.js)]

閉じる、または、パネルを折りたたみ、`_doClose()`メソッドを実行する必要があります。 したがって、ユーザーは、2 番目のボタンをクリックすると、次の JavaScript コードが呼び出されます。

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample7.js)]

3 番目のボタンは、パネルの状態を切り替えます: から折りたたまれているを展開すると、その逆です。 `CollapsiblePanelExtender`公開、`toggle()`ようメソッド: パネルの状態を反転させます。 ただしも別の方法 (によって内部的に使用される、`toggle()`メソッド):`get_Collapsed()`のメソッド、`CollapsiblePanelExtender()`パネルが折りたたまれているかどうかどうかがわかります。 この関数の戻り値は、パネルが展開されているいずれか (`_doOpen()`メソッド) か、折りたたんだ状態 (`_doClose()`) メソッド。

[!code-javascript[Main](collapsing-and-expanding-a-panel-from-javascript-cs/samples/sample8.js)]


[![3 番目のボタン、パネルの状態を変更する: から拡張とバックのために折りたたむ](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image2.png)](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image1.png)

3 番目のボタン、パネルの状態を変更する: から拡張とバックのために折りたたむ ([フルサイズのイメージを表示するをクリックして](collapsing-and-expanding-a-panel-from-javascript-cs/_static/image3.png))

> [!div class="step-by-step"]
> [次へ](collapsing-and-expanding-a-panel-from-javascript-vb.md)
