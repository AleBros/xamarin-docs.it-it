---
title: Colore di sfondo della cella in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che imposta il colore di sfondo predefinito delle celle in iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68651382"
---
# <a name="cell-background-color-on-ios"></a>Colore di sfondo della cella in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma iOS imposta il colore di sfondo predefinito di [`Cell`](xref:Xamarin.Forms.Cell) istanze. Viene utilizzato in XAML impostando la proprietà `Cell.DefaultBackgroundColor` associabile su un [`Color`](xref:Xamarin.Forms.Color):

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

In alternativa, è possibile C# usare l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

Il metodo `ListView.On<iOS>` specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il metodo `Cell.SetDefaultBackgroundColor`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , imposta il colore di sfondo della cella su un [`Color`](xref:Xamarin.Forms.Color)specificato. Inoltre, è possibile utilizzare il metodo `Cell.DefaultBackgroundColor` per recuperare il colore di sfondo della cella corrente.

Il risultato è che il colore di sfondo in un [`Cell`](xref:Xamarin.Forms.Cell) può essere impostato su uno specifico [`Color`](xref:Xamarin.Forms.Color):

[![Screenshot delle celle di intestazione del gruppo alzavola in iOS](cell-background-color-images/group-header-cell-color.png "ListView con celle di intestazione del gruppo verde acqua")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView con celle di intestazione del gruppo verde acqua")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
