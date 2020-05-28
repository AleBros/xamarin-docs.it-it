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
ms.openlocfilehash: 46e8dec3d5644defdeb8a2265a73815adfde92d8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136034"
---
# <a name="listview-group-header-style-on-ios"></a>Stile intestazione gruppo ListView in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se [`ListView`](xref:Xamarin.Forms.ListView) le celle di intestazione fluttuano durante lo scorrimento. Viene utilizzato in XAML impostando la `ListView.GroupHeaderStyle` proprietà associabile su un valore dell' `GroupHeaderStyle` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
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

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

Il `ListView.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `ListView.SetGroupHeaderStyle` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare se [`ListView`](xref:Xamarin.Forms.ListView) le celle dell'intestazione fluttuano durante lo scorrimento. L' `GroupHeaderStyle` enumerazione fornisce due valori possibili:

- `Plain`: indica che le celle di intestazione sono fluttuate quando è stato eseguito lo [`ListView`](xref:Xamarin.Forms.ListView) scorrimento (impostazione predefinita).
- `Grouped`: indica che le celle di intestazione non sono float quando viene eseguito lo [`ListView`](xref:Xamarin.Forms.ListView) scorrimento di.

Inoltre, il `ListView.GetGroupHeaderStyle` metodo può essere utilizzato per restituire l'oggetto `GroupHeaderStyle` applicato a [`ListView`](xref:Xamarin.Forms.ListView) .

Il risultato è che un `GroupHeaderStyle` valore specificato viene applicato a [`ListView`](xref:Xamarin.Forms.ListView) , che controlla se le celle di intestazione fluttuano durante lo scorrimento:

[![Screenshot delle celle di intestazione ListView mobili e non mobili, in iOS](listview-group-header-style-images/group-header-styles.png "ListView con celle di intestazione mobili e non mobili")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con celle di intestazione mobili e non mobili")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
