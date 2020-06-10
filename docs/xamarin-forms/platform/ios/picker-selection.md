---
title: "selezione elementi selezione in iOS" Descrizione: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo in una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla quando la selezione degli elementi si verifica in una selezione ".
ms. prod: Novell MS. AssetID: 26B0604A-BD30-49FD-83A6-F0EDFBB0524B ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="picker-item-selection-on-ios"></a>Selezione elemento selezione in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questi controlli specifici della piattaforma iOS quando si verifica la selezione degli elementi in un oggetto [`Picker`](xref:Xamarin.Forms.Picker) , consentendo all'utente di specificare che la selezione degli elementi si verifica quando si esplorano gli elementi nel controllo o solo dopo aver premuto il pulsante **fine** . Viene utilizzato in XAML impostando la `Picker.UpdateMode` proprietà associata su un valore dell' `UpdateMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

Il `Picker.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Picker.SetUpdateMode` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare quando si verifica la selezione dell'elemento, con l' `UpdateMode` enumerazione che fornisce due valori possibili:

- `Immediately`-la selezione degli elementi si verifica quando l'utente Sfoglia gli elementi in [`Picker`](xref:Xamarin.Forms.Picker) . Si tratta del comportamento predefinito in Xamarin.Forms .
- `WhenFinished`-la selezione degli elementi si verifica solo dopo che l'utente ha premuto il pulsante **done** nell' [`Picker`](xref:Xamarin.Forms.Picker) .

Inoltre, il `SetUpdateMode` metodo può essere utilizzato per abilitare o disabilitare i valori di enumerazione chiamando il `UpdateMode` metodo, che restituisce l'oggetto corrente `UpdateMode` :

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Il risultato è che un oggetto specificato `UpdateMode` viene applicato a [`Picker`](xref:Xamarin.Forms.Picker) , che controlla quando si verifica la selezione dell'elemento:

[![](picker-selection-images/picker-updatemode.png "Picker UpdateMode Platform-Specific")](picker-selection-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
