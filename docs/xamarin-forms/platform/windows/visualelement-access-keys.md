---
title: VisualElement tasti di Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Windows specifico della piattaforma che consente di specificare una chiave di accesso per un VisualElement.
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f14014b257ee5061b6dd074719c3ca27577c6013
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209336"
---
# <a name="visualelement-access-keys-on-windows"></a>VisualElement tasti di Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Chiavi di accesso sono scelte rapide da tastiera che consentono di migliorare l'usabilità e accessibilità dell'App in Universal Windows Platform (UWP), fornendo un modo intuitivo per gli utenti di passare rapidamente e interagire con l'interfaccia utente visibile dell'app tramite la tastiera anziché tramite tocco o un mouse. Sono combinazioni di uno o più tasti alfanumerici, in genere premuti in sequenza e il tasto Alt. Tasti di scelta rapida sono automaticamente supportati per le chiavi di accesso che utilizzano un singolo carattere alfanumerico.

Suggerimenti tasto di accesso sono variabili badge visualizzati accanto ai controlli che includono chiavi di accesso. Ogni suggerimento di chiavi di accesso contiene le chiavi di caratteri alfanumeriche che attiva il controllo associato. Quando un utente preme il tasto Alt, vengono visualizzati i suggerimenti tasto di accesso.

Questo specifico della piattaforma UWP viene usato per specificare una chiave di accesso per un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Vengono utilizzati in XAML, impostando il [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) proprietà associata a un valore alfanumerico e, facoltativamente impostando il [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) proprietà associata a un valore di [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumerazione, il [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) proprietà associata una `double`e i [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) proprietà associata a un `double`:

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

![Specifica della piattaforma di chiavi di accesso di VisualElement](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement accesso chiavi specifiche della piattaforma")

Quando un utente attiva di una chiave di accesso, premendo il tasto Alt seguito dall'accesso della chiave, l'azione predefinita per il `VisualElement` verrà eseguito. Ad esempio, quando un utente attiva il tasto di scelta rapida in una [ `Switch` ](xref:Xamarin.Forms.Switch), il `Switch` è attivata o disattivata. Quando un utente attiva la chiave di accesso in un' [ `Entry` ](xref:Xamarin.Forms.Entry), il `Entry` Ottiene lo stato attivo. Quando un utente attiva il tasto di scelta rapida in una [ `Button` ](xref:Xamarin.Forms.Button), il gestore eventi per il [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) viene eseguito l'evento.

Per altre informazioni sulle chiavi di accesso, vedere [chiavi di accesso](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
