---
title: Direzione di pull di RefreshView in Windows
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare l'oggetto specifico della piattaforma Windows che consente di modificare la direzione di pull di un RefreshView.
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46a1b4d00b9eea276b9a3b3d5bffbdac3d31e0ef
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136578"
---
# <a name="refreshview-pull-direction-on-windows"></a>Direzione di pull di RefreshView in Windows

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa piattaforma UWP (Universal Windows Platform) specifica della piattaforma consente di modificare la direzione di pull di un oggetto in `RefreshView` modo che corrisponda all'orientamento del controllo scorrevole che Visualizza i dati. Viene utilizzato in XAML impostando la `RefreshView.RefreshPullDirection` proprietà associabile su un valore dell' `RefreshPullDirection` enumerazione:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <RefreshView windows:RefreshView.RefreshPullDirection="LeftToRight"
                 IsRefreshing="{Binding IsRefreshing}"
                 Command="{Binding RefreshCommand}">
        <ScrollView>
            ...
        </ScrollView>
    </RefreshView>
 </ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

Il `RefreshView.On<Windows>` metodo specifica che questa specifica della piattaforma verrà eseguita solo sul piattaforma UWP (Universal Windows Platform). Il `RefreshView.SetRefreshPullDirection` metodo, nello [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi, viene usato per impostare la direzione di pull di `RefreshView` , con l' `RefreshPullDirection` enumerazione che fornisce quattro valori possibili:

- `LeftToRight`indica che un pull da sinistra a destra avvia un aggiornamento.
- `TopToBottom`indica che un pull dall'alto verso il basso avvia un aggiornamento e rappresenta la direzione di pull predefinita di un oggetto `RefreshView` .
- `RightToLeft`indica che un pull da destra a sinistra avvia un aggiornamento.
- `BottomToTop`indica che un pull dalla parte inferiore alla parte superiore avvia un aggiornamento.

Inoltre, il `GetRefreshPullDirection` metodo può essere utilizzato per restituire l'oggetto corrente `RefreshPullDirection` dell'oggetto `RefreshView` .

Il risultato è che un oggetto specificato `RefreshPullDirection` viene applicato a `RefreshView` , per impostare la direzione di pull in modo che corrisponda all'orientamento del controllo scorrevole che Visualizza i dati. La schermata seguente mostra un oggetto `RefreshView` con una `LeftToRight` direzione di pull:

[![Screenshot di un RefreshView con direzione di pull da sinistra a destra, in UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView con direzione di pull da sinistra a destra")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView con direzione di pull da sinistra a destra")

> [!NOTE]
> Quando si modifica la direzione di pull, la posizione iniziale del cerchio di avanzamento ruota automaticamente in modo che la freccia si avvii nella posizione appropriata per la direzione di pull.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
