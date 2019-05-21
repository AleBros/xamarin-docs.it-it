---
title: Stile dell'intestazione di gruppo ListView in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che controlla se le celle di intestazione ListView float durante lo scorrimento.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: e2f6a402d1df8b842e147ab145474688c203aaa6
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925873"
---
# <a name="listview-group-header-style-on-ios"></a>Stile dell'intestazione di gruppo ListView in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Questo controlli specifici della piattaforma iOS se [ `ListView` ](xref:Xamarin.Forms.ListView) le celle di intestazione float durante lo scorrimento. Vengono utilizzati in XAML, impostando il `ListView.GroupHeaderStyle` proprietà associabili su un valore del `GroupHeaderStyle` enumerazione:

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

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `ListView.SetGroupHeaderStyle` metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene utilizzato per controllare se [ `ListView` ](xref:Xamarin.Forms.ListView) le celle di intestazione float durante lo scorrimento. Il `GroupHeaderStyle` enumerazione fornisce due valori possibili:

- `Plain` : indica che le celle di intestazione float quando la [ `ListView` ](xref:Xamarin.Forms.ListView) viene eseguito lo scorrimento (impostazione predefinita).
- `Grouped` : indica che le celle di intestazione float non quando le [ `ListView` ](xref:Xamarin.Forms.ListView) di scorrimento.

Inoltre, il `ListView.GetGroupHeaderStyle` metodo può essere utilizzato per restituire il `GroupHeaderStyle` che viene applicato al [ `ListView` ](xref:Xamarin.Forms.ListView).

Il risultato è che un oggetto specificato `GroupHeaderStyle` valore viene applicato per il [ `ListView` ](xref:Xamarin.Forms.ListView), che controlla se le celle di intestazione float durante lo scorrimento:

[![Screenshot della fissa e a virgola mobile ListView le celle di intestazione, in iOS](listview-group-header-style-images/group-header-styles.png "ListView con le celle di intestazione fissa e a virgola mobile")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView con le celle di intestazione fissa e a virgola mobile")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
