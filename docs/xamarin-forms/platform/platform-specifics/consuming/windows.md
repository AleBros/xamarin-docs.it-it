---
title: Specifiche delle piattaforme di Windows
description: "Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di Windows che vengono compilate in xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: da9279939af8dc4033cd89769a7add60a745ac85
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="windows-platform-specifics"></a>Specifiche delle piattaforme di Windows

_Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di Windows che vengono compilate in xamarin. Forms._

Nel Windows piattaforma UWP (Universal), xamarin. Forms contiene le specifiche di piattaforma seguenti:

- Opzioni di posizionamento della barra degli strumenti. Per ulteriori informazioni, vedere [modifica la posizione della barra degli strumenti](#toolbar_placement).
- Un parzialmente comprimibile [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) barra di spostamento. Per ulteriori informazioni, vedere [compressione di una barra di navigazione MasterDetailPage](#collapsable_navigation_bar).

<a name="toolbar_placement" />

## <a name="changing-the-toolbar-placement"></a>Modifica la posizione della barra degli strumenti

Questo specifico della piattaforma viene utilizzato per modificare la posizione di una barra degli strumenti in un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)e viene utilizzata in XAML impostando i [ `Page.ToolbarPlacement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty/) proprietà associata a un valore del [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) enumerazione:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>

```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

Il `Page.On<Windows>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Windows. Il [ `Page.SetToolbarPlacement` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) dello spazio dei nomi, viene utilizzato per impostare il posizionamento della barra degli strumenti, con la [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) fornendo enumerazione tre valori: [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default/), [ `Top` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top/), e [ `Bottom` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom/).

Il risultato è che la posizione della barra degli strumenti specificata viene applicata al [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) istanza:

[![](windows-images/toolbar-placement.png "Barra degli strumenti posizione specifica della piattaforma")](windows-images/toolbar-placement-large.png "specifico della piattaforma posizionamento della barra degli strumenti")

<a name="collapsable_navigation_bar" />

## <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Compressione di una barra di navigazione MasterDetailPage

Questo specifico della piattaforma viene utilizzato per comprimere la barra di spostamento in un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)e viene utilizzata in XAML impostando i [ `MasterDetailPage.CollapseStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty/) e [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty/)le proprietà associate:

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

Il `MasterDetailPage.On<Windows>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Windows. Il [ `Page.SetCollapseStyle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) dello spazio dei nomi, viene utilizzato per specificare lo stile di compressione, con la [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) enumerazione fornendo due valori: [ `Full` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full/) e [ `Partial` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial/). Il [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/System.Double/) consente di specificare la larghezza di una barra di spostamento compressa parzialmente.

Il risultato è che un oggetto specificato [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) è collegato il [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) istanza, con la larghezza specificata anche:

[![](windows-images/collapsed-navigation-bar.png "Specifico della piattaforma barra di spostamento compressa")](windows-images/collapsed-navigation-bar-large.png "compresso specifico della piattaforma barra di spostamento")

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di Windows che vengono compilate in xamarin. Forms. Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di piattaforma specifiche](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
