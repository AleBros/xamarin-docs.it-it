---
title: Colore di sfondo della cella in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che imposta il colore di sfondo predefinito delle celle in iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "68651382"
---
# <a name="cell-background-color-on-ios"></a>Colore di sfondo della cella in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS imposta il colore di sfondo predefinito [`Cell`](xref:Xamarin.Forms.Cell) delle istanze. Viene utilizzato in XAML impostando la `Cell.DefaultBackgroundColor` proprietà associabile su un oggetto: [`Color`](xref:Xamarin.Forms.Color)

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Cell.SetDefaultBackgroundColor` metodo, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) nello spazio dei nomi, imposta il colore di sfondo della cella [`Color`](xref:Xamarin.Forms.Color)su un oggetto specificato. Inoltre, il `Cell.DefaultBackgroundColor` metodo può essere utilizzato per recuperare il colore di sfondo della cella corrente.

Il risultato è che il colore di sfondo di [`Cell`](xref:Xamarin.Forms.Cell) un oggetto può essere impostato su [`Color`](xref:Xamarin.Forms.Color)uno specifico:

[![Screenshot delle celle di intestazione del gruppo alzavola in iOS](cell-background-color-images/group-header-cell-color.png "ListView con celle di intestazione del gruppo verde") acqua](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView con celle di intestazione del gruppo verde acqua")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
