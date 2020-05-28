---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1bfd61e79a2b4697e884afb45e4b9080ee939b87
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136526"
---
# <a name="visualelement-access-keys-on-windows"></a>Chiavi di accesso di visualElement per Windows

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Le chiavi di accesso sono scelte rapide da tastiera che migliorano l'usabilità e l'accessibilità delle app nel piattaforma UWP (Universal Windows Platform) (UWP), offrendo agli utenti un modo intuitivo per esplorare rapidamente e interagire con l'interfaccia utente visibile dell'app tramite una tastiera anziché tramite tocco o mouse. Sono combinazioni del tasto ALT e di una o più chiavi alfanumeriche, in genere premuti in sequenza. Le scelte rapide da tastiera sono supportate automaticamente per le chiavi di accesso che utilizzano un singolo carattere alfanumerico.

I suggerimenti per la chiave di accesso sono notifiche mobili visualizzate accanto ai controlli che includono chiavi di accesso. Ogni suggerimento chiave di accesso contiene i tasti alfanumerici che attivano il controllo associato. Quando un utente preme il tasto ALT, vengono visualizzati i suggerimenti per la chiave di accesso.

Questo specifico della piattaforma UWP viene usato per specificare una chiave di accesso per un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Viene utilizzato in XAML impostando la [`VisualElement.AccessKey`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) proprietà associata su un valore alfanumerico e, facoltativamente, impostando la [`VisualElement.AccessKeyPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) proprietà associata su un valore dell' [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) enumerazione, la [`VisualElement.AccessKeyHorizontalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) proprietà associata a un oggetto `double` e la [`VisualElement.AccessKeyVerticalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) proprietà associata a un oggetto `double` :

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

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

Il `VisualElement.On<Windows>` metodo specifica che questa specifica della piattaforma verrà eseguita solo sul piattaforma UWP (Universal Windows Platform). [ `VisualElement.SetAccessKey` ] (Xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. SetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Il metodo visualElement}, System. String)), nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, viene usato per impostare il valore della chiave di accesso per `VisualElement` . [ `VisualElement.SetAccessKeyPlacement` ] (Xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. SetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Oggetti visivi}, Xamarin.Forms . AccessKeyPlacement)), consente di specificare la posizione da utilizzare per la visualizzazione del suggerimento della chiave di accesso, con l' [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) enumerazione che fornisce i valori possibili seguenti:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto): indica che il posizionamento del suggerimento per la chiave di accesso sarà determinato dal sistema operativo.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top): indica che il suggerimento chiave di accesso verrà visualizzato sopra il bordo superiore del `VisualElement` .
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom): indica che il suggerimento chiave di accesso verrà visualizzato sotto il bordo inferiore di `VisualElement` .
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right): indica che il suggerimento chiave di accesso verrà visualizzato a destra del bordo destro di `VisualElement` .
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left): indica che il suggerimento chiave di accesso verrà visualizzato a sinistra del bordo sinistro dell'oggetto `VisualElement` .
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center): indica che il suggerimento chiave di accesso verrà visualizzato sovrapposto al centro di `VisualElement` .

> [!NOTE]
> In genere, il [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) posizionamento del suggerimento chiave è sufficiente, che include il supporto per le interfacce utente adattive.

[ `VisualElement.SetAccessKeyHorizontalOffset` ] (Xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. SetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Oggetti visivi}, System. Double) e [ `VisualElement.SetAccessKeyVerticalOffset` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. SetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Metodi visivi}, System. Double)) possono essere utilizzati per un controllo più granulare del percorso del suggerimento chiave di accesso. L'argomento del `SetAccessKeyHorizontalOffset` metodo indica la distanza di spostamento della mancia del tasto di scelta a sinistra o a destra e l'argomento al `SetAccessKeyVerticalOffset` metodo indica la distanza di spostamento del suggerimento per la chiave di accesso verso l'alto o verso il basso.

>[!NOTE]
> Non è possibile impostare gli offset del suggerimento chiave di accesso quando è impostato il posizionamento della chiave di accesso `Auto` .

Inoltre, [ `GetAccessKey` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. GetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})), [ `GetAccessKeyPlacement` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. GetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})), [ `GetAccessKeyHorizontalOffset` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. GetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})) e [ `GetAccessKeyVerticalOffset` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. visualElement. GetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})) i metodi possono essere usati per recuperare un valore della chiave di accesso e la relativa posizione.

Il risultato è che i suggerimenti per la chiave di accesso possono essere visualizzati accanto a qualsiasi [`VisualElement`](xref:Xamarin.Forms.VisualElement) istanza che definisce le chiavi di accesso, premendo il tasto Alt:

![Chiavi di accesso di visualElement specifiche della piattaforma](visualelement-access-keys-images/visualelement-accesskeys.png "Chiavi di accesso di visualElement specifiche della piattaforma")

Quando un utente attiva una chiave di accesso, premendo il tasto Alt seguito dal tasto di opzione, viene eseguita l'azione predefinita per il `VisualElement` . Ad esempio, quando un utente attiva il tasto di accesso su un oggetto [`Switch`](xref:Xamarin.Forms.Switch) , `Switch` viene attivato o disattivato. Quando un utente attiva il tasto di accesso su un oggetto [`Entry`](xref:Xamarin.Forms.Entry) , `Entry` ottiene lo stato attivo. Quando un utente attiva il tasto di accesso su [`Button`](xref:Xamarin.Forms.Button) , viene eseguito il gestore eventi per l' [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento.

Per ulteriori informazioni sulle chiavi di accesso, vedere [chiavi di accesso](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
