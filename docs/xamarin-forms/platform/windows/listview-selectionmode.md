---
title: SelectionMode di ListView in Windows
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare l'oggetto specifico della piattaforma Windows che controlla se gli elementi in un controllo ListView possono rispondere ai movimenti di tocco.
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f6a90a8a0397db99a245f706450e7dc83097a45e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656907"
---
# <a name="listview-selectionmode-on-windows"></a>SelectionMode di ListView in Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Nella piattaforma Windows universale, per impostazione predefinita di Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) nativi `ItemClick` evento a cui rispondere interazione, anziché l'oggetto nativo `Tapped` evento. Fornisce funzionalità di accessibilità in modo che l'Assistente vocale di Windows e della tastiera possano interagire con il `ListView`. Tuttavia, esegue il rendering anche tutti i movimenti tocco all'interno di `ListView` è inutilizzabile.

Questo piattaforma UWP (Universal Windows Platform) controlla se gli elementi in un [`ListView`](xref:Xamarin.Forms.ListView) oggetto possono rispondere ai movimenti Tap e, di conseguenza, se il nativo `ListView` genera l' `ItemClick` evento o. `Tapped` Vengono utilizzati in XAML, impostando il [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) proprietà associata a un valore del [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

Il `ListView.On<Windows>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo nella piattaforma Windows universale. Il [`ListView.SetSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) metodo, nel [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi, viene utilizzato per controllare se gli elementi in un [`ListView`](xref:Xamarin.Forms.ListView) può rispondere per la scelta di movimenti, con il [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumerazione che fornisce due valori possibili:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) : indica che il `ListView` genereranno nativo `ItemClick` eventi per gestire l'interazione e pertanto offrono funzionalità di accessibilità. Pertanto, l'Assistente vocale di Windows e della tastiera possono interagire con il `ListView`. Tuttavia, gli elementi nel `ListView` non può rispondere per la scelta di movimenti. Si tratta del comportamento predefinito per `ListView` istanze sulla piattaforma Windows universale.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) : indica che il `ListView` genereranno nativo `Tapped` eventi per gestire l'interazione. Di conseguenza, gli elementi di `ListView` può rispondere per la scelta di movimenti. Tuttavia, non vi è alcuna funzionalità di accessibilità e di conseguenza l'Assistente vocale di Windows e la tastiera non è possibile interagire con il `ListView`.

> [!NOTE]
> Il `Accessible` e `Inaccessible` modalità di selezione si escludono a vicenda e sarà necessario scegliere tra un accessibile [`ListView`](xref:Xamarin.Forms.ListView) o un `ListView` che può rispondere a toccare i movimenti.

Inoltre, il [`GetSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) metodo può essere utilizzato per restituire l'oggetto corrente [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

Il risultato è che un oggetto specificato [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) viene applicato al [`ListView`](xref:Xamarin.Forms.ListView), che controlla se gli elementi nel `ListView` può rispondere per la scelta di movimenti e pertanto se nativo `ListView` viene attivato il `ItemClick` o `Tapped` evento.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
