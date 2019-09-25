---
title: Stile intestazione gruppo ListView in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se le celle delle intestazioni ListView sono fluttuanti durante lo scorrimento.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "68648315"
---
# <a name="listview-group-header-style-on-ios"></a>Stile intestazione gruppo ListView in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se [`ListView`](xref:Xamarin.Forms.ListView) le celle di intestazione fluttuano durante lo scorrimento. Viene utilizzato in XAML impostando la `ListView.GroupHeaderStyle` proprietà associabile su un valore `GroupHeaderStyle` dell'enumerazione:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `ListView.SetGroupHeaderStyle` metodo, nello spazio [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dei nomi, viene utilizzato per controllare [`ListView`](xref:Xamarin.Forms.ListView) se le celle dell'intestazione fluttuano durante lo scorrimento. L' `GroupHeaderStyle` enumerazione fornisce due valori possibili:

- `Plain`: indica che le celle di intestazione sono [`ListView`](xref:Xamarin.Forms.ListView) fluttuate quando è stato eseguito lo scorrimento (impostazione predefinita).
- `Grouped`: indica che le celle di intestazione non sono float [`ListView`](xref:Xamarin.Forms.ListView) quando viene eseguito lo scorrimento di.

Inoltre, il `ListView.GetGroupHeaderStyle` metodo può essere utilizzato per restituire l'oggetto `GroupHeaderStyle` applicato a. [`ListView`](xref:Xamarin.Forms.ListView)

Il risultato è che un valore `GroupHeaderStyle` specificato viene applicato [`ListView`](xref:Xamarin.Forms.ListView)a, che controlla se le celle di intestazione fluttuano durante lo scorrimento:

[![Screenshot delle celle di intestazione ListView mobili e non mobili, in iOS](listview-group-header-style-images/group-header-styles.png "ListView con celle di intestazione mobili e non mobili")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con celle di intestazione mobili e non mobili")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
