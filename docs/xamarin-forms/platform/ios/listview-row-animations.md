---
title: ListView righe animazioni in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che controlla se le animazioni di riga sono disabilitate quando viene aggiornata la raccolta di elementi ListView.
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: 50480f5b21c6f0c855ff6f9aa22b6126c6a6787c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945693"
---
# <a name="listview-row-animations-on-ios"></a>ListView righe animazioni in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo controlli specifici della piattaforma iOS riga indica se le animazioni sono disabilitate quando il [ `ListView` ](xref:Xamarin.Forms.ListView) raccolta di elementi da aggiornare. Vengono utilizzati in XAML, impostando il `ListView.RowAnimationsEnabled` la proprietà associabile per `false`:

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

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `ListView.SetRowAnimationsEnabled` (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se le animazioni di riga sono disabilitata quando il [ `ListView` ](xref:Xamarin.Forms.ListView) raccolta di elementi in corso di aggiornamento. Inoltre, il `ListView.GetRowAnimationsEnabled` metodo può essere utilizzato da restituire se le animazioni di riga sono disabilitate nel `ListView`.

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) Per impostazione predefinita, vengono abilitate animazioni di riga. Pertanto, un'animazione si verifica quando viene inserita una nuova riga in un `ListView`.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
