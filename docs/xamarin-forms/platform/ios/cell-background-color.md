---
title: Colore di sfondo delle celle in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifico della piattaforma che consente di impostare il colore di sfondo delle celle in iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 396e674bb3e5642f7c54455ee9e30ba5bf232f18
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61030076"
---
# <a name="cell-background-color-on-ios"></a>Colore di sfondo delle celle in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma iOS imposta il colore di sfondo predefinito [ `Cell` ](xref:Xamarin.Forms.Cell) istanze. Vengono utilizzati in XAML, impostando il `Cell.DefaultBackgroundColor` la proprietà associabile per una [ `Color` ](xref:Xamarin.Forms.Color):

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

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Cell.SetDefaultBackgroundColor` metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, imposta il colore di sfondo della cella specificata [ `Color` ](xref:Xamarin.Forms.Color). Inoltre, il `Cell.DefaultBackgroundColor` metodo può essere usato per recuperare il colore di sfondo cella corrente.

Il risultato è che il colore di sfondo in un [ `Cell` ](xref:Xamarin.Forms.Cell) può essere impostato su uno specifico [ `Color` ](xref:Xamarin.Forms.Color):

[![Screenshot delle celle dell'intestazione gruppo verde acqua, in iOS](cell-background-color-images/group-header-cell-color.png "ListView con le celle di intestazione di gruppo verde acqua")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView con le celle di intestazione di gruppo verde acqua")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
