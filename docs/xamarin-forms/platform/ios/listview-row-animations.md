---
title: "ListView Row Animation on iOS" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se le animazioni di riga sono disabilitate durante l'aggiornamento della raccolta di elementi ListView. "
ms. prod: Novell MS. AssetID: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 02/21/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-row-animations-on-ios"></a>Animazioni di riga ListView in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se le animazioni di riga vengono disabilitate durante l' [`ListView`](xref:Xamarin.Forms.ListView) aggiornamento della raccolta di elementi. Viene utilizzato in XAML impostando la `ListView.RowAnimationsEnabled` proprietà associabile su `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

Il `ListView.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `ListView.SetRowAnimationsEnabled` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare se le animazioni di riga vengono disabilitate durante l' [`ListView`](xref:Xamarin.Forms.ListView) aggiornamento della raccolta di elementi. Inoltre, il `ListView.GetRowAnimationsEnabled` metodo può essere utilizzato per restituire se le animazioni di riga sono disabilitate in `ListView` .

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)le animazioni di riga sono abilitate per impostazione predefinita. Pertanto, un'animazione si verifica quando viene inserita una nuova riga in un oggetto `ListView` .

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
