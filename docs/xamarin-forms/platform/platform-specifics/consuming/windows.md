---
title: Funzionalità specifiche della piattaforma Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare il Windows-funzionalità specifiche della piattaforma incorporate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 2130bbbedbab66fac9427947ca42f21c346360ce
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "38998416"
---
# <a name="windows-platform-specifics"></a>Funzionalità specifiche della piattaforma Windows

_Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare il Windows-funzionalità specifiche della piattaforma incorporate in xamarin. Forms._

## <a name="visualelements"></a>VisualElements

Nella piattaforma Windows universale, le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni, pagine e layout di xamarin. Forms:

- L'impostazione di una chiave di accesso per un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [chiavi di accesso di impostazione VisualElement](#visualelement-accesskeys).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [disattivazione modalità colore Legacy](#legacy-color-mode).

<a name="visualelement-accesskeys" />

### <a name="setting-visualelement-access-keys"></a>Impostazione delle chiavi di accesso VisualElement

Chiavi di accesso sono scelte rapide da tastiera che consentono di migliorare l'usabilità e accessibilità delle App sulla piattaforma Windows universale, fornendo un modo intuitivo per gli utenti di passare rapidamente e interagire con l'interfaccia utente visibile dell'app tramite la tastiera anziché tramite tocco o un oggetto mouse. Sono combinazioni di uno o più tasti alfanumerici, in genere premuti in sequenza e il tasto Alt. Tasti di scelta rapida sono automaticamente supportati per le chiavi di accesso che utilizzano un singolo carattere alfanumerico.

Suggerimenti tasto di accesso sono variabili badge visualizzati accanto ai controlli che includono chiavi di accesso. Ogni suggerimento di chiavi di accesso contiene le chiavi di caratteri alfanumeriche che attiva il controllo associato. Quando un utente preme il tasto Alt, vengono visualizzati i suggerimenti tasto di accesso.

Questo specifico della piattaforma viene usato per specificare una chiave di accesso per un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Vengono utilizzati in XAML, impostando il [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) proprietà associata a un valore alfanumerico e, facoltativamente impostando il [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) proprietà associata a un valore di [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumerazione, il [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) proprietà associata una `double`e i [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) proprietà associata a un `double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

Il `VisualElement.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene utilizzato per impostare il valore di chiave di accesso per il `VisualElement`. Il [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement)) metodo, facoltativamente, specifica la posizione da utilizzare per visualizzare il suggerimento di chiavi di accesso, con il [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumerazione che fornisce i valori possibili seguenti:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) : indica che il posizionamento di base al suggerimento tasto di accesso verrà determinato dal sistema operativo.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) : indica che il suggerimento di chiavi di accesso verrà visualizzato sopra il bordo superiore del `VisualElement`.
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) : indica che il suggerimento di chiavi di accesso verrà visualizzato sotto il bordo inferiore del `VisualElement`.
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) : indica che il suggerimento di chiavi di accesso verrà visualizzato a destra del bordo destro del `VisualElement`.
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) : indica che il suggerimento di chiavi di accesso verrà visualizzato a sinistra del bordo sinistro del `VisualElement`.
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) : indica che il suggerimento di chiavi di accesso verrà visualizzato sovrapposta al centro del `VisualElement`.

> [!NOTE]
> In genere, il [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto) selezione host di base al suggerimento tasto è sufficiente, che include il supporto per interfacce utente adattiva.

Il [ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) e [ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) metodi possono essere utilizzati per un controllo più granulare del percorso di base al suggerimento tasto di accesso. L'argomento per il `SetAccessKeyHorizontalOffset` metodo indica come distanza di spostamento a sinistra il suggerimento di chiavi di accesso o a destra e l'argomento di `SetAccessKeyVerticalOffset` metodo indica la distanza di spostamento il suggerimento di chiavi di accesso verso l'alto o verso il basso.

>[!NOTE]
> Offset di base al suggerimento tasto di accesso non può essere impostato quando è impostato il posizionamento di chiavi di accesso `Auto`.

Inoltre, il [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), e [ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) metodi possono essere usati per recuperare un accesso valore e il percorso della chiave.

Il risultato è che i suggerimenti tasto di accesso possono essere visualizzati accanto alle [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) accessibile a istanze che definiscono le chiavi, premendo il tasto Alt:

![Specifica della piattaforma di chiavi di accesso di VisualElement](windows-images/visualelement-accesskeys.png "VisualElement accesso chiavi specifiche della piattaforma")

Quando un utente attiva di una chiave di accesso, premendo il tasto Alt seguito dall'accesso della chiave, l'azione predefinita per il `VisualElement` verrà eseguito. Ad esempio, quando un utente attiva il tasto di scelta rapida in una [ `Switch` ](xref:Xamarin.Forms.Switch), il `Switch` è attivata o disattivata. Quando un utente attiva la chiave di accesso in un' [ `Entry` ](xref:Xamarin.Forms.Entry), il `Entry` Ottiene lo stato attivo. Quando un utente attiva il tasto di scelta rapida in una [ `Button` ](xref:Xamarin.Forms.Button), il gestore eventi per il [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) viene eseguito l'evento.

Per altre informazioni sulle chiavi di accesso, vedere [chiavi di accesso](/windows/uwp/design/input/access-keys#key-tip-positioning).

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>La disabilitazione della modalità di colore precedente

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

## <a name="views"></a>Visualizzazioni

In Universal Windows Platform (UWP), le funzionalità specifiche della piattaforma seguenti viene fornita per le visualizzazioni di xamarin. Forms:

- Rilevamento di ordine di lettura dal testo contenuto nel [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) istanze. Per altre informazioni, vedere [rilevamento di ordine di lettura dal contenuto](#inputview-readingorder).
- Abilitazione del supporto di movimenti tocco in una [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [abilitazione supporto movimenti toccare in un ListView](#listview-selectionmode).
- Abilitazione di un [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) per interagire con il motore di controllo ortografico. Per altre informazioni, vedere [abilitazione controllo ortografico SearchBar](#searchbar-spellcheck).
- Abilitazione di un [ `WebView` ](xref:Xamarin.Forms.WebView) per visualizzare gli avvisi di JavaScript in una finestra di dialogo messaggio UWP. Per altre informazioni, vedere [visualizzazione di avvisi JavaScript](#webview-javascript-alert).

<a name="inputview-readingorder" />

### <a name="detecting-reading-order-from-content"></a>Rilevamento di ordine di lettura dal contenuto

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

<a name="listview-selectionmode" />

### <a name="enabling-tap-gesture-support-in-a-listview"></a>Abilitazione del supporto di movimenti tocco in un ListView

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

<a name="searchbar-spellcheck" />

### <a name="enabling-searchbar-spell-check"></a>Abilitazione controllo ortografico SearchBar

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

<a name="webview-javascript-alert" />

### <a name="displaying-javascript-alerts"></a>La visualizzazione degli avvisi di JavaScript

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

## <a name="pages"></a>Pages

Nella piattaforma Windows universale, le funzionalità specifiche della piattaforma seguenti viene fornita per le pagine di xamarin. Forms:

- Comprimere il [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) barra di spostamento. Per altre informazioni, vedere [compressione di una barra di spostamento MasterDetailPage](#collapsable_navigation_bar).
- Impostazione delle opzioni di posizionamento della barra degli strumenti. Per altre informazioni, vedere [modifica la posizione della barra degli strumenti pagina](#toolbar_placement).
- Abilitazione di icone da visualizzare in una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) sulla barra degli strumenti. Per altre informazioni, vedere [abilitazione icone su una TabbedPage](#tabbedpage-icons).

<a name="collapsable_navigation_bar" />

### <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Compressione di una barra di navigazione MasterDetailPage

Questo specifico della piattaforma è utilizzato per comprimere la barra di spostamento in un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)e vengono utilizzati in XAML impostando i [ `MasterDetailPage.CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty) e [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty)le proprietà associate:

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

Il `MasterDetailPage.On<Windows>` metodo consente di specificare che questo specifico della piattaforma sarà eseguita solo su Windows. Il [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usato per specificare lo stile di compressione, con la [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) enumerazione che fornisce due valori: [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) e [ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial). Il [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double)) metodo viene utilizzato per specificare la larghezza di una barra di spostamento compressa parzialmente.

Il risultato è che un oggetto specificato [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) viene applicato per il [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) istanza, con la larghezza specificata anche:

[![](windows-images/collapsed-navigation-bar.png "Specifiche della piattaforma sulla barra di spostamento compressa")](windows-images/collapsed-navigation-bar-large.png#lightbox "compresso barra di spostamento specifici della piattaforma")

<a name="toolbar_placement" />

### <a name="changing-the-page-toolbar-placement"></a>Modifica il posizionamento degli strumenti della pagina

Questo specifico della piattaforma viene utilizzata per modificare la posizione di una barra degli strumenti in un [ `Page` ](xref:Xamarin.Forms.Page)e vengono utilizzati in XAML, impostando il [ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) proprietà associata a un valore del [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumerazione:

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

Il `Page.On<Windows>` metodo consente di specificare che questo specifico della piattaforma sarà eseguita solo su Windows. Il [ `Page.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)) (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene usato per impostare la posizione della barra degli strumenti, con la [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumerazione fornendo tre valori: [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default), [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top), e [ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom).

Il risultato è che la posizione della barra degli strumenti specificata viene applicata per il [ `Page` ](xref:Xamarin.Forms.Page) istanza:

[![](windows-images/toolbar-placement.png "Sulla barra degli strumenti posizione specifici della piattaforma")](windows-images/toolbar-placement-large.png#lightbox "specifiche della piattaforma posizionamento della barra degli strumenti")

<a name="tabbedpage-icons" />

### <a name="enabling-icons-on-a-tabbedpage"></a>Abilitazione delle icone su una TabbedPage

Questo specifico della piattaforma Abilita le icone di pagina da visualizzare in una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) sulla barra degli strumenti e offre la possibilità di specificare facoltativamente le dimensioni dell'icona. Vengono utilizzati in XAML, impostando il [ `TabbedPage.HeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) proprietà associata `true`e, facoltativamente impostando il [ `TabbedPage.HeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) proprietà associata una [ `Size` ](xref:Xamarin.Forms.Size) valore:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" Icon="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" Icon="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" Icon="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
    {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", Icon = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", Icon = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", Icon = "contacts.png" });
  }
}
```

Il `TabbedPage.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [ `TabbedPage.SetHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean)) metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene utilizzato per attivare o disattivare la icone di intestazione. Il [ `TabbedPage.SetHeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size)) metodo facoltativamente specifica le dimensioni dell'icona intestazione con un [ `Size` ](xref:Xamarin.Forms.Size) valore.

Inoltre, il `TabbedPage` classe nel `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` dello spazio dei nomi ha anche un [ `EnableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) metodo che abilita le icone di intestazione, un [ `DisableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) metodo che consente di disattivare le icone di intestazione, e una [ `IsHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) metodo che restituisce un `boolean` valore che indica se sono abilitate le icone di intestazione.

Il risultato è tale pagina è possibile visualizzare le icone in una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra degli strumenti con le dimensioni dell'icona facoltativamente impostata in base alle esigenze:

![Specifiche della piattaforma icone abilitate TabbedPage](windows-images/tabbedpage-icons.png "TabbedPage icone abilitate specifico della piattaforma")

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare il Windows-funzionalità specifiche della piattaforma incorporate in xamarin. Forms. Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
