---
title: Specifiche delle piattaforme di Windows
description: Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di Windows che vengono compilate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 7299de658a3491928e9bbeaa4dd192a8e95c435e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732801"
---
# <a name="windows-platform-specifics"></a>Specifiche delle piattaforme di Windows

_Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di Windows che vengono compilate in xamarin. Forms._

Nel Windows piattaforma UWP (Universal), xamarin. Forms contiene le specifiche di piattaforma seguenti:

- Impostazione delle opzioni di posizionamento della barra degli strumenti. Per ulteriori informazioni, vedere [modifica la posizione della barra degli strumenti](#toolbar_placement).
- Comprimere il [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) barra di spostamento. Per ulteriori informazioni, vedere [compressione di una barra di navigazione MasterDetailPage](#collapsable_navigation_bar).
- Abilitazione di un [ `WebView` ](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi di JavaScript in una finestra di messaggio UWP. Per altre informazioni, vedere [visualizzazione di avvisi JavaScript](#webview-javascript-alert).
- Abilitazione di un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) per interagire con il motore di controllo ortografico. Per altre informazioni, vedere [attivazione controllo ortografia SearchBar](#searchbar-spellcheck).
- Rilevamento di ordine di lettura dal testo contenuto in [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze. Per altre informazioni, vedere [il rilevamento di ordine di lettura dal contenuto](#inputview-readingorder).
- Disabilitare la modalità legacy di colore in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [la disabilitazione di colore Legacy](#legacy-color-mode).
- Attivazione del supporto dei movimenti tocco in un [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [abilitazione supporto movimenti toccare in un controllo ListView](#listview-selectionmode).

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

[![](windows-images/toolbar-placement.png "Barra degli strumenti posizione specifica della piattaforma")](windows-images/toolbar-placement-large.png#lightbox "specifico della piattaforma posizionamento della barra degli strumenti")

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

[![](windows-images/collapsed-navigation-bar.png "Specifico della piattaforma barra di spostamento compressa")](windows-images/collapsed-navigation-bar-large.png#lightbox "compresso specifico della piattaforma barra di spostamento")

<a name="webview-javascript-alert" />

## <a name="displaying-javascript-alerts"></a>La visualizzazione degli avvisi di JavaScript

Questo specifico della piattaforma consente una [ `WebView` ](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi di JavaScript in una finestra di messaggio UWP. Viene utilizzata in XAML impostando i [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) associata a un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

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

Il `WebView.On<Windows>` metodo consente di specificare che questo specifico per la piattaforma verrà eseguito solo sulla piattaforma Windows universale. Il [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) metodo nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se sono abilitati gli avvisi di JavaScript. Inoltre, il `WebView.SetIsJavaScriptAlertEnabled` metodo può essere utilizzato per attivare o disattivare gli avvisi di JavaScript chiamando il [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) per restituire se sono abilitate:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

Il risultato è che è possono visualizzare gli avvisi di JavaScript in una finestra di dialogo UWP messaggio:

![Avviso di WebView JavaScript specifici per la piattaforma](windows-images/webview-javascript-alert.png "WebView JavaScript avviso specifico della piattaforma")

<a name="searchbar-spellcheck" />

## <a name="enabling-searchbar-spell-check"></a>Abilitare il controllo ortografico SearchBar

Questo specifico della piattaforma consente una [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) per interagire con il motore di controllo ortografico. Viene utilizzata in XAML impostando i [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) associata a un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

Il `SearchBar.On<Windows>` metodo consente di specificare che questo specifico per la piattaforma verrà eseguito solo sulla piattaforma Windows universale. Il [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) attiva e lo spazio dei nomi, disattiva il controllo ortografico. Inoltre, il `SearchBar.SetIsSpellCheckEnabled` metodo può essere utilizzato per attivare o disattivare il controllo ortografico chiamando il [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) per restituire se il correttore ortografico è abilitato:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

Il risultato è che il testo inserito il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) può essere il controllo ortografia, con un'ortografia errata viene indicata che l'utente:

![SearchBar ortografico controllo specifico per la piattaforma](windows-images/searchbar-spellcheck.png "SearchBar ortografico controllo specifico per la piattaforma")

> [!NOTE]
> Il `SearchBar` classe il [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dispone di spazio dei nomi [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) e [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) metodi che possono essere utilizzati per abilitare e disabilitare controllo ortografico nel `SearchBar`, rispettivamente.

<a name="inputview-readingorder" />

## <a name="detecting-reading-order-from-content"></a>Rilevamento di ordine di lettura dal contenuto

Questo specifico della piattaforma consente l'ordine di lettura (da sinistra a destra o da destra a sinistra) di testo bidirezionale [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze per il rilevamento in modo dinamico. Viene utilizzata in XAML impostando i [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (per `Entry` e `Editor` istanze) o [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) associata a un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

Il `Editor.On<Windows>` metodo consente di specificare che questo specifico per la piattaforma verrà eseguito solo sulla piattaforma Windows universale. Il [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se viene rilevato l'ordine di lettura dal contenuto di [ `InputView` ](xref:Xamarin.Forms.InputView). Inoltre, il `InputView.SetDetectReadingOrderFromContent` metodo può essere utilizzato per attivare / disattivare l'ordine di lettura viene rilevato dal contenuto chiamando il [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) per restituire il valore corrente:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

Il risultato è che [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze possono avere l'ordine di lettura del contenuto rilevato in modo dinamico:

[![Rilevamento di ordine di lettura dal contenuto specifico della piattaforma InputView](windows-images/editor-readingorder.png "InputView rilevamento di ordine di lettura dal contenuto specifico della piattaforma")](windows-images/editor-readingorder-large.png#lightbox "InputView rilevamento di ordine di lettura da contenuto specifico della piattaforma")

> [!NOTE]
> A differenza di impostazione di [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) proprietà, la logica per le viste che rilevano l'ordine di lettura dal contenuto di testo non altera l'allineamento del testo all'interno della visualizzazione. Al contrario, modifica l'ordine in cui sono disposti i blocchi di testo bidirezionale.

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Disabilitare la modalità colore Legacy

Alcune delle viste di xamarin. Forms offrono una modalità colore legacy. In questa modalità, quando il [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) della visualizzazione è impostata su `false`, la vista sostituirà i colori impostati dall'utente con i colori predefiniti nativi per lo stato disabilitato. Per garantire la compatibilità, questa modalità colore legacy rimarrà con le versioni precedenti il comportamento predefinito per le visualizzazioni supportate.

Questo specifico della piattaforma disattiva questa modalità colore legacy, in modo che i colori impostato su una vista dall'utente rimangano anche quando la visualizzazione è disabilitata. Viene utilizzata in XAML impostando i [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) associata a `false`:

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

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<Windows>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Windows. Il [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usata per controllare se la modalità legacy è disabilitata. Inoltre, il [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) metodo può essere utilizzato per restituire se la modalità legacy è disabilitata.

Il risultato è che è possibile disabilitare la modalità legacy di colore, in modo che rimangano anche colori impostato su una vista dall'utente quando viene disattivata la vista:

![](windows-images/legacy-color-mode-disabled.png "Modalità legacy colore disabilitata")

> [!NOTE]
> Durante l'impostazione di un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) su una vista, la modalità legacy colore viene ignorata completamente. Per ulteriori informazioni sugli stati visivi, vedere [il gestore degli stati Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="listview-selectionmode" />

## <a name="enabling-tap-gesture-support-in-a-listview"></a>Attivazione del supporto dei movimenti tocco in un controllo ListView

Nella piattaforma Windows universale, per impostazione predefinita di xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) nativi `ItemClick` evento rispondere all'interazione, anziché nativo `Tapped` evento. Fornisce funzionalità di accessibilità in modo che l'Assistente vocale Windows e la tastiera può interagire con il `ListView`. Tuttavia, esegue il rendering anche tutti i movimenti tocco all'interno di `ListView` è inutilizzabile.

Questo controlli specifici della piattaforma se gli elementi in un [ `ListView` ](xref:Xamarin.Forms.ListView) può rispondere per toccare i movimenti e pertanto se nativo `ListView` viene attivato il `ItemClick` o `Tapped` evento. Viene utilizzata in XAML impostando il [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) un valore della proprietà associata di [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione:

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

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

Il `ListView.On<Windows>` metodo consente di specificare che questo specifico per la piattaforma verrà eseguito solo sulla piattaforma Windows universale. Il [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, consente di controllare se gli elementi in un [ `ListView` ](xref:Xamarin.Forms.ListView) può rispondere per toccare i movimenti, con il [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione fornendo due valori possibili:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) : indica che il `ListView` genereranno nativo `ItemClick` eventi per gestire l'interazione e pertanto fornisce funzionalità di accessibilità. Pertanto, l'Assistente vocale Windows e la tastiera può interagire con il `ListView`. Tuttavia, gli elementi nel `ListView` non può rispondere per toccare i movimenti. Si tratta del comportamento predefinito per `ListView` istanze nella piattaforma Windows universale.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) : indica che il `ListView` genereranno nativo `Tapped` eventi per gestire l'interazione. Pertanto, gli elementi nel `ListView` può rispondere per toccare i movimenti. Tuttavia, non vi è alcuna funzionalità di accessibilità e pertanto l'Assistente vocale Windows e della tastiera non è possibile interagire con il `ListView`.

> [!NOTE]
> Il `Accessible` e `Inaccessible` modalità di selezione si escludono a vicenda e sarà necessario effettuare una scelta tra un accessibile [ `ListView` ](xref:Xamarin.Forms.ListView) o `ListView` che può rispondere a toccare i movimenti.

Inoltre, il [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) metodo può essere utilizzato per restituire l'oggetto corrente [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

Il risultato consiste nel fatto che un oggetto specificato [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) si riferisce al [ `ListView` ](xref:Xamarin.Forms.ListView), che controlla se gli elementi nel `ListView` può rispondere per toccare i movimenti e pertanto se nativo `ListView` viene attivato il `ItemClick` o `Tapped` evento.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare la piattaforma-specifiche di Windows che vengono compilate in xamarin. Forms. Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
