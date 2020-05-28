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
ms.openlocfilehash: 594e436c9db7c123fea4f9aa262c9d27af765b07
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136006"
---
# <a name="listview-row-animations-on-ios"></a>Animazioni di riga ListView in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se le animazioni di riga vengono disabilitate durante l' [`ListView`](xref:Xamarin.Forms.ListView) aggiornamento della raccolta di elementi. Viene utilizzato in XAML impostando la `ListView.RowAnimationsEnabled` proprietà associabile su `false` :

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

Il `ListView.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `ListView.SetRowAnimationsEnabled` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare se le animazioni di riga vengono disabilitate durante l' [`ListView`](xref:Xamarin.Forms.ListView) aggiornamento della raccolta di elementi. Inoltre, il `ListView.GetRowAnimationsEnabled` metodo può essere utilizzato per restituire se le animazioni di riga sono disabilitate in `ListView` .

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)le animazioni di riga sono abilitate per impostazione predefinita. Pertanto, un'animazione si verifica quando viene inserita una nuova riga in un oggetto `ListView` .

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
