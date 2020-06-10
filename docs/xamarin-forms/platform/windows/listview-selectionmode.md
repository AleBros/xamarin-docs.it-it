---
title: "ListView SelectionMode in Windows" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. In questo articolo viene illustrato come utilizzare la piattaforma Windows specifica che controlla se gli elementi in un controllo ListView possono rispondere ai movimenti Tap ".
ms. prod: Novell MS. AssetID: 57EF3A7F-1407-4B31-AE21-D149293D4228 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-selectionmode-on-windows"></a>SelectionMode di ListView in Windows

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Nel piattaforma UWP (Universal Windows Platform), per impostazione predefinita, Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) utilizza l' `ItemClick` evento nativo per rispondere all'interazione, anziché l'evento nativo `Tapped` . Questo fornisce funzionalità di accessibilità in modo che l'Assistente vocale di Windows e la tastiera possano interagire con `ListView` . Tuttavia, viene anche eseguito il rendering dei movimenti di tocco all'interno di `ListView` inutilizzabile.

Questo piattaforma UWP (Universal Windows Platform) controlla se gli elementi in un oggetto [`ListView`](xref:Xamarin.Forms.ListView) possono rispondere ai movimenti Tap e, di conseguenza, se il nativo `ListView` genera l' `ItemClick` `Tapped` evento o. Viene utilizzato in XAML impostando la [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) proprietà associata su un valore dell' [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione:

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

Il `ListView.On<Windows>` metodo specifica che questa specifica della piattaforma verrà eseguita solo sul piattaforma UWP (Universal Windows Platform). [ `ListView.SetSelectionMode` ] (Xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. seselectionmode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView}, Xamarin.Forms . Il metodo PlatformConfiguration. WindowsSpecific. ListViewSelectionMode), nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, viene usato per controllare se gli elementi in un oggetto [`ListView`](xref:Xamarin.Forms.ListView) possono rispondere ai movimenti Tap, con l' [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione che fornisce due valori possibili:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible): indica che il `ListView` genererà l' `ItemClick` evento nativo per gestire l'interazione e pertanto fornirà la funzionalità di accessibilità. Pertanto, l'Assistente vocale di Windows e la tastiera possono interagire con `ListView` . Tuttavia, gli elementi in `ListView` non possono rispondere ai movimenti Tap. Si tratta del comportamento predefinito per `ListView` le istanze nel piattaforma UWP (Universal Windows Platform).
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible): indica che l' `ListView` evento nativo viene attivato `Tapped` per gestire l'interazione. Gli elementi in possono pertanto `ListView` rispondere ai movimenti Tap. Tuttavia, non è disponibile alcuna funzionalità di accessibilità e pertanto l'Assistente vocale di Windows e la tastiera non possono interagire con il `ListView` .

> [!NOTE]
> Le modalità di selezione e si escludono a `Accessible` `Inaccessible` vicenda ed è necessario scegliere tra un oggetto accessibile [`ListView`](xref:Xamarin.Forms.ListView) o un `ListView` che può rispondere ai movimenti Tap.

Inoltre, [ `GetSelectionMode` ] (xrif: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. getSelectionMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView})) il metodo può essere usato per restituire l'oggetto corrente [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) .

Il risultato è che un oggetto specificato [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) viene applicato a [`ListView`](xref:Xamarin.Forms.ListView) , che controlla se gli elementi in `ListView` possono rispondere ai movimenti Tap e, di conseguenza, se il nativo `ListView` genera `ItemClick` l' `Tapped` evento o.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
