---
title: Stile intestazione gruppo ListView in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se le celle delle intestazioni ListView sono fluttuanti durante lo scorrimento.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648315"
---
# <a name="listview-group-header-style-on-ios"></a>Stile intestazione gruppo ListView in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se [`ListView`](xref:Xamarin.Forms.ListView) le celle di intestazione fluttuano durante lo scorrimento. Viene utilizzato in XAML impostando la proprietà `ListView.GroupHeaderStyle` associabile su un valore dell'enumerazione `GroupHeaderStyle`:

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

In alternativa, è possibile C# usare l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

Il metodo `ListView.On<iOS>` specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il metodo `ListView.SetGroupHeaderStyle`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , viene utilizzato per controllare se [`ListView`](xref:Xamarin.Forms.ListView) celle di intestazione fluttuano durante lo scorrimento. L'enumerazione `GroupHeaderStyle` fornisce due valori possibili:

- `Plain`: indica che le celle di intestazione fluttuano quando viene eseguito lo scorrimento della [`ListView`](xref:Xamarin.Forms.ListView) (impostazione predefinita).
- `Grouped`: indica che le celle di intestazione non vengono fluttuate quando viene eseguito lo scorrimento del [`ListView`](xref:Xamarin.Forms.ListView) .

Inoltre, è possibile usare il metodo `ListView.GetGroupHeaderStyle` per restituire l'`GroupHeaderStyle` applicato al [`ListView`](xref:Xamarin.Forms.ListView).

Il risultato è che un valore `GroupHeaderStyle` specificato viene applicato al [`ListView`](xref:Xamarin.Forms.ListView), che controlla se le celle di intestazione fluttuano durante lo scorrimento:

[![Screenshot delle celle di intestazione ListView mobili e non mobili, in iOS](listview-group-header-style-images/group-header-styles.png "ListView con celle di intestazione mobili e non mobili")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con celle di intestazione mobili e non mobili")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
