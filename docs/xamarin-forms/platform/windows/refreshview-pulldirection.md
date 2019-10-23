---
title: RefreshView direzione pull in Windows
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare l'oggetto specifico della piattaforma Windows che consente di modificare la direzione di pull di un RefreshView.
ms.prod: xamarin
ms.assetid: 407A862B-281E-4384-9696-C0655830B84D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: cf2ab38bed7b45a48fcf0b5f86add49c0d4cc21f
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697318"
---
# <a name="refreshview-pull-direction-on-windows"></a>RefreshView direzione pull in Windows

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa piattaforma UWP (Universal Windows Platform) specifica della piattaforma consente di modificare la direzione del pull di un `RefreshView` in modo che corrisponda all'orientamento del controllo scorrevole che Visualizza i dati. Viene utilizzato in XAML impostando la proprietà `RefreshView.RefreshPullDirection` associabile su un valore dell'enumerazione `RefreshPullDirection`:

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

In alternativa, è possibile C# usare l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...
refreshView.On<Windows>().SetRefreshPullDirection(RefreshPullDirection.LeftToRight);
```

Il metodo `RefreshView.On<Windows>` specifica che questa specifica della piattaforma verrà eseguita solo nel piattaforma UWP (Universal Windows Platform). Il metodo `RefreshView.SetRefreshPullDirection`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) , viene usato per impostare la direzione di pull della `RefreshView`, con l'enumerazione `RefreshPullDirection` che fornisce quattro valori possibili:

- `LeftToRight` indica che un pull da sinistra a destra avvia un aggiornamento.
- `TopToBottom` indica che un pull dall'alto verso il basso avvia un aggiornamento e rappresenta la direzione predefinita del pull di un `RefreshView`.
- `RightToLeft` indica che un pull da destra a sinistra avvia un aggiornamento.
- `BottomToTop` indica che un pull dalla parte inferiore alla parte superiore avvia un aggiornamento.

Inoltre, è possibile utilizzare il metodo `GetRefreshPullDirection` per restituire la `RefreshPullDirection` corrente del `RefreshView`.

Il risultato è che al `RefreshView` viene applicata una `RefreshPullDirection` specificata per impostare la direzione del pull in modo che corrisponda all'orientamento del controllo scorrevole che Visualizza i dati. Lo screenshot seguente mostra un `RefreshView` con una direzione di pull `LeftToRight`:

[![Screenshot di un RefreshView con direzione di pull da sinistra a destra, in UWP](refreshview-pulldirection-images/refreshview-pulldirection.png "RefreshView con direzione di pull da sinistra a destra")](refreshview-pulldirection-images/refreshview-pulldirection-large.png#lightbox "RefreshView con direzione di pull da sinistra a destra")

> [!NOTE]
> Quando si modifica la direzione di pull, la posizione iniziale del cerchio di avanzamento ruota automaticamente in modo che la freccia si avvii nella posizione appropriata per la direzione di pull.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
