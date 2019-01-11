---
title: Fast ListView lo scorrimento in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che consente lo scorrimento rapido di dati in un ListView.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: d4b4a72d2bfe77c433c17fa9f427a95121855e01
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209216"
---
# <a name="listview-fast-scrolling-on-android"></a>Fast ListView lo scorrimento in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma Android consente di abilitare lo scorrimento veloce dei dati in un [ `ListView` ](xref:Xamarin.Forms.ListView). Vengono utilizzati in XAML, impostando il `ListView.IsFastScrollEnabled` proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

Il `ListView.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `ListView.SetIsFastScrollEnabled` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per abilitare lo scorrimento rapido di dati in un [ `ListView` ](xref:Xamarin.Forms.ListView). Inoltre, il `SetIsFastScrollEnabled` metodo può essere utilizzato per attivare o disattivare lo scorrimento veloce chiamando il `IsFastScrollEnabled` metodo restituisca se lo scorrimento veloce è abilitato:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

Il risultato è che lo scorrimento veloce tra i dati in un [ `ListView` ](xref:Xamarin.Forms.ListView) può essere abilitata, che modifica la dimensione dello scorrimento:

[![](listview-fast-scrolling-images/fastscroll.png "Specifica della piattaforma FastScroll ListView")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll specifiche della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
