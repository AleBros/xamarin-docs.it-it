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
ms.openlocfilehash: 90282262926fef663183be247e37d64dd1be9124
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138567"
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
