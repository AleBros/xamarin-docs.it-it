---
title: Funzionalità specifiche della piattaforma Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare il Windows-funzionalità specifiche della piattaforma incorporate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 52895564ef327845940d687a58b007fb1502e62b
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935121"
---
# <a name="windows-platform-specifics"></a>Funzionalità specifiche della piattaforma Windows

_Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare il Windows-funzionalità specifiche della piattaforma incorporate in xamarin. Forms._

In Universal Windows Platform (UWP), xamarin. Forms contiene le funzionalità specifiche della piattaforma seguenti:

- Impostazione delle opzioni di posizionamento della barra degli strumenti. Per altre informazioni, vedere [modifica la posizione della barra degli strumenti](#toolbar_placement).
- Comprimere il [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) barra di spostamento. Per altre informazioni, vedere [compressione di una barra di spostamento MasterDetailPage](#collapsable_navigation_bar).
- Abilitazione di un [ `WebView` ](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi di JavaScript in una finestra di dialogo messaggio UWP. Per altre informazioni, vedere [visualizzazione di avvisi JavaScript](#webview-javascript-alert).
- Abilitazione di un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) per interagire con il motore di controllo ortografico. Per altre informazioni, vedere [abilitazione controllo ortografico SearchBar](#searchbar-spellcheck).
- Rilevamento di ordine di lettura dal testo contenuto nel [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze. Per altre informazioni, vedere [rilevamento di ordine di lettura dal contenuto](#inputview-readingorder).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [disattivazione modalità colore Legacy](#legacy-color-mode).
- Abilitazione del supporto di movimenti tocco in una [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [abilitazione supporto movimenti toccare in un ListView](#listview-selectionmode).

<a name="toolbar_placement" />

## <a name="changing-the-toolbar-placement"></a>Modifica la posizione della barra degli strumenti

Questo specifico della piattaforma viene utilizzata per modificare la posizione di una barra degli strumenti in un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)e vengono utilizzati in XAML, impostando il [ `Page.ToolbarPlacement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty/) proprietà associata a un valore del [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) enumerazione:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

Il `Page.On<Windows>` metodo consente di specificare che questo specifico della piattaforma sarà eseguita solo su Windows. Il [ `Page.SetToolbarPlacement` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usato per impostare la posizione della barra degli strumenti, con la [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumerazione fornendo tre valori: [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default), [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top), e [ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom).

Il risultato è che la posizione della barra degli strumenti specificata viene applicata per il [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) istanza:

[![](windows-images/toolbar-placement.png "Sulla barra degli strumenti posizione specifici della piattaforma")](windows-images/toolbar-placement-large.png#lightbox "specifiche della piattaforma posizionamento della barra degli strumenti")

<a name="collapsable_navigation_bar" />

## <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Compressione di una barra di navigazione MasterDetailPage

Questo specifico della piattaforma è utilizzato per comprimere la barra di spostamento in un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)e vengono utilizzati in XAML impostando i [ `MasterDetailPage.CollapseStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty/) e [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty/)le proprietà associate:

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

Il `MasterDetailPage.On<Windows>` metodo consente di specificare che questo specifico della piattaforma sarà eseguita solo su Windows. Il [ `Page.SetCollapseStyle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) dello spazio dei nomi, viene usato per specificare lo stile di compressione, con la [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) enumerazione che fornisce due valori: [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) e [ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial). Il [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/System.Double/) metodo viene utilizzato per specificare la larghezza di una barra di spostamento compressa parzialmente.

Il risultato è che un oggetto specificato [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) viene applicato per il [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) istanza, con la larghezza specificata anche:

[![](windows-images/collapsed-navigation-bar.png "Specifiche della piattaforma sulla barra di spostamento compressa")](windows-images/collapsed-navigation-bar-large.png#lightbox "compresso barra di spostamento specifici della piattaforma")

<a name="webview-javascript-alert" />

## <a name="displaying-javascript-alerts"></a>La visualizzazione degli avvisi di JavaScript

Questo specifico della piattaforma consente a un [ `WebView` ](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi di JavaScript in una finestra di dialogo messaggio UWP. Vengono utilizzati in XAML, impostando il [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

Il `WebView.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se sono abilitati gli avvisi di JavaScript. Inoltre, il `WebView.SetIsJavaScriptAlertEnabled` metodo può essere utilizzato per attivare o disattivare gli avvisi di JavaScript chiamando il [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) metodo restituisca se sono abilitati:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

Il risultato è che è possono visualizzare gli avvisi di JavaScript in una finestra di dialogo messaggio UWP:

![Avviso di JavaScript di WebView specifiche della piattaforma](windows-images/webview-javascript-alert.png "avviso JavaScript di WebView specifiche della piattaforma")

<a name="searchbar-spellcheck" />

## <a name="enabling-searchbar-spell-check"></a>Abilitazione controllo ortografico SearchBar

Questo specifico della piattaforma consente a un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) per interagire con il motore di controllo ortografico. Vengono utilizzati in XAML, impostando il [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

Il `SearchBar.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, attiva il controllo ortografico e disattiva. Inoltre, il `SearchBar.SetIsSpellCheckEnabled` metodo può essere utilizzato per attivare o disattivare il controllo ortografico chiamando il [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) metodo da restituire se il correttore ortografico è abilitato:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

Il risultato è che il testo immesso nel [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) può essere il controllo ortografia, con un'ortografia errata viene indicata che l'utente:

![SearchBar ortografico controllo specifico per la piattaforma](windows-images/searchbar-spellcheck.png "SearchBar ortografico controllo specifico per la piattaforma")

> [!NOTE]
> Il `SearchBar` classe la [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi contiene inoltre [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) e [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) metodi che possono essere usati per abilitare e disabilitare controllo ortografico nel `SearchBar`, rispettivamente.

<a name="inputview-readingorder" />

## <a name="detecting-reading-order-from-content"></a>Rilevamento di ordine di lettura dal contenuto

Questo specifico della piattaforma consente l'ordine di lettura (da sinistra a destra o right-to-left) del testo bidirezionale [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze devono essere rilevate in modo dinamico. Vengono utilizzati in XAML, impostando il [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (per `Entry` e `Editor` istanze) o [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

Il `Editor.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se l'ordine di lettura viene rilevato dal contenuto il [ `InputView` ](xref:Xamarin.Forms.InputView). Inoltre, il `InputView.SetDetectReadingOrderFromContent` metodo può essere utilizzato per attivare / disattivare l'ordine di lettura viene rilevato dal contenuto chiamando il [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) per restituire il valore corrente:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

Il risultato è che [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze possono avere l'ordine di lettura dei propri contenuti rilevati in modo dinamico:

[![InputView rilevamento di ordine di lettura dal contenuto specifico della piattaforma](windows-images/editor-readingorder.png "InputView rilevamento di ordine di lettura dal contenuto specifico della piattaforma")](windows-images/editor-readingorder-large.png#lightbox "InputView rilevamento di ordine di lettura da contenuto specifico della piattaforma")

> [!NOTE]
> A differenza di impostazione la [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà, la logica per le visualizzazioni che rilevano l'ordine di lettura dal contenuto di testo non influiranno l'allineamento del testo all'interno della visualizzazione. Al contrario, modifica l'ordine in cui sono disposti i blocchi di testo bidirezionale.

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>La disabilitazione della modalità di colore precedente

Alcune delle viste di xamarin. Forms offrono una modalità colore legacy. In questa modalità, quando la [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) della visualizzazione è impostata su `false`, la visualizzazione sostituiranno i colori impostati dall'utente con i colori nativo predefinito per lo stato disabilitato. Per garantire la compatibilità, questa modalità legacy colore rimane con le versioni precedenti il comportamento predefinito per le visualizzazioni supportate.

Questo specifico della piattaforma disabilita questa modalità colore legacy, in modo che i colori impostati su una vista dall'utente rimangano anche quando la visualizzazione è disabilitata. Vengono utilizzati in XAML, impostando il [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) proprietà associata `false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<Windows>` metodo consente di specificare che questo specifico della piattaforma sarà eseguita solo su Windows. Il [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se la modalità legacy è disabilitata. Inoltre, il [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) metodo può essere utilizzato da restituire se la modalità legacy è disabilitata.

Il risultato è che può essere disabilitata la modalità legacy, in modo che rimangano anche i colori impostati su una vista dall'utente quando la visualizzazione è disabilitata:

![](windows-images/legacy-color-mode-disabled.png "Modalità legacy colore disabilitata")

> [!NOTE]
> Quando si imposta una [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) in una vista, la modalità legacy di colori viene completamente ignorata. Per altre informazioni sugli stati visual, vedere [di xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="listview-selectionmode" />

## <a name="enabling-tap-gesture-support-in-a-listview"></a>Abilitazione del supporto di movimenti tocco in un ListView

Nella piattaforma Windows universale, per impostazione predefinita di xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) nativi `ItemClick` evento a cui rispondere interazione, anziché l'oggetto nativo `Tapped` evento. Fornisce funzionalità di accessibilità in modo che l'Assistente vocale di Windows e della tastiera possano interagire con il `ListView`. Tuttavia, esegue il rendering anche tutti i movimenti tocco all'interno di `ListView` è inutilizzabile.

Questo controlli specifici della piattaforma se gli elementi in un [ `ListView` ](xref:Xamarin.Forms.ListView) può rispondere per la scelta di movimenti e pertanto se nativo `ListView` viene attivato il `ItemClick` o `Tapped` evento. Vengono utilizzati in XAML, impostando il [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) proprietà associata a un valore del [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

Il `ListView.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene utilizzato per controllare se gli elementi in un [ `ListView` ](xref:Xamarin.Forms.ListView) può rispondere per la scelta di movimenti, con il [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione che fornisce due valori possibili:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) : indica che il `ListView` genereranno nativo `ItemClick` eventi per gestire l'interazione e pertanto offrono funzionalità di accessibilità. Pertanto, l'Assistente vocale di Windows e della tastiera possono interagire con il `ListView`. Tuttavia, gli elementi nel `ListView` non può rispondere per la scelta di movimenti. Si tratta del comportamento predefinito per `ListView` istanze sulla piattaforma Windows universale.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) : indica che il `ListView` genereranno nativo `Tapped` eventi per gestire l'interazione. Di conseguenza, gli elementi di `ListView` può rispondere per la scelta di movimenti. Tuttavia, non vi è alcuna funzionalità di accessibilità e di conseguenza l'Assistente vocale di Windows e la tastiera non è possibile interagire con il `ListView`.

> [!NOTE]
> Il `Accessible` e `Inaccessible` modalità di selezione si escludono a vicenda e sarà necessario scegliere tra un accessibile [ `ListView` ](xref:Xamarin.Forms.ListView) o un `ListView` che può rispondere a toccare i movimenti.

Inoltre, il [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) metodo può essere utilizzato per restituire l'oggetto corrente [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

Il risultato è che un oggetto specificato [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) viene applicato al [ `ListView` ](xref:Xamarin.Forms.ListView), che controlla se gli elementi nel `ListView` può rispondere per la scelta di movimenti e pertanto se nativo `ListView` viene attivato il `ItemClick` o `Tapped` evento.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare il Windows-funzionalità specifiche della piattaforma incorporate in xamarin. Forms. Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
