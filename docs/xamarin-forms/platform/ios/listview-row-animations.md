---
title: Animazioni di riga ListView in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se le animazioni di riga sono disabilitate durante l'aggiornamento della raccolta di elementi ListView.
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: 9e44c22e670847102cf0bc0f79a860abcac30760
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652827"
---
# <a name="listview-row-animations-on-ios"></a>Animazioni di riga ListView in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se le animazioni di riga vengono disabilitate durante l'aggiornamento della raccolta di [`ListView`](xref:Xamarin.Forms.ListView) elementi. Vengono utilizzati in XAML, impostando il `ListView.RowAnimationsEnabled` la proprietà associabile per `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
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

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `ListView.SetRowAnimationsEnabled` metodo, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) nello spazio dei nomi, viene utilizzato per controllare se le animazioni di riga vengono [`ListView`](xref:Xamarin.Forms.ListView) disabilitate durante l'aggiornamento della raccolta di elementi. Inoltre, il `ListView.GetRowAnimationsEnabled` metodo può essere utilizzato per restituire se le animazioni di riga sono disabilitate `ListView`in.

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)le animazioni di riga sono abilitate per impostazione predefinita. Pertanto, un'animazione si verifica quando viene inserita una nuova riga in `ListView`un oggetto.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
