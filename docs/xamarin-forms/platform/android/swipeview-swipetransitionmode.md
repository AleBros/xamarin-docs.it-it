---
title: Modalità di transizione SwipeView scorrere in Android
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. In questo articolo viene illustrato come utilizzare la piattaforma Android specifica che controlla la transizione utilizzata durante l'apertura di un SwipeView.
ms.prod: xamarin
ms.assetid: 6B1F8213-9D62-4C40-9F04-881F1371B5AA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c420fe65b020067169230dd06dbcd5ce65c036ab
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128622"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>Modalità di transizione SwipeView scorrere in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android controlla la transizione usata durante l'apertura di un oggetto `SwipeView` . Viene utilizzato in XAML impostando la `SwipeView.SwipeTransitionMode` proprietà associabile su un valore dell' `SwipeTransitionMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core" >
    <StackLayout>
        <SwipeView android:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

Il `SwipeView.On<Android>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in Android. Il `SwipeView.SetSwipeTransitionMode` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare la transizione utilizzata durante l'apertura di un oggetto `SwipeView` . L' `SwipeTransitionMode` enumerazione fornisce due valori possibili:

- `Reveal`indica che gli elementi swipe verranno rivelati quando il contenuto viene sottomesso a `SwipeView` scorrimento e rappresenta il valore predefinito della `SwipeView.SwipeTransitionMode` Proprietà.
- `Drag`indica che gli elementi swipe verranno trascinati nella visualizzazione mentre viene visualizzato il `SwipeView` contenuto del dito.

Inoltre, il `SwipeView.GetSwipeTransitionMode` metodo può essere utilizzato per restituire l'oggetto `SwipeTransitionMode` applicato a `SwipeView` .

Il risultato è che un `SwipeTransitionMode` valore specificato viene applicato a `SwipeView` , che controlla la transizione utilizzata durante l'apertura di `SwipeView` :

[![Screenshot di SwipeView SwipeTransitionModes, in Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes in Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes in Android")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
