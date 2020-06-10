---
title: "colore di sfondo della cella in iOS" Descrizione: "le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che imposta il colore di sfondo predefinito delle celle in iOS. "
ms. prod: Novell MS. AssetID: 2A3FDACF-5AE2-40DE-8488-6FE41733712F ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="cell-background-color-on-ios"></a>Colore di sfondo della cella in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS imposta il colore di sfondo predefinito delle [`Cell`](xref:Xamarin.Forms.Cell) istanze. Viene utilizzato in XAML impostando la `Cell.DefaultBackgroundColor` proprietà associabile su un oggetto [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  IsGroupingEnabled="true">
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <ViewCell ios:Cell.DefaultBackgroundColor="Teal">
                        <Label Margin="10,10"
                               Text="{Binding Key}"
                               FontAttributes="Bold" />
                    </ViewCell>
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
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

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

Il `ListView.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Cell.SetDefaultBackgroundColor` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, imposta il colore di sfondo della cella su un oggetto specificato [`Color`](xref:Xamarin.Forms.Color) . Inoltre, il `Cell.DefaultBackgroundColor` metodo può essere utilizzato per recuperare il colore di sfondo della cella corrente.

Il risultato è che il colore di sfondo di un oggetto [`Cell`](xref:Xamarin.Forms.Cell) può essere impostato su uno specifico [`Color`](xref:Xamarin.Forms.Color) :

[![Screenshot delle celle di intestazione del gruppo alzavola in iOS](cell-background-color-images/group-header-cell-color.png "ListView con celle di intestazione del gruppo verde acqua")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView con celle di intestazione del gruppo verde acqua")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
