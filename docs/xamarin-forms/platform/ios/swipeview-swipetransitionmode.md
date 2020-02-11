---
title: Modalità di transizione SwipeView scorrere in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla la transizione usata per l'apertura di un SwipeView.
ms.prod: xamarin
ms.assetid: C667F24C-BAD8-47E0-9285-D3546BEF703B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: d5ba92d008cf3431bce2c197aca45c894eb3d5c7
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490325"
---
# <a name="swipeview-swipe-transition-mode-on-ios"></a>Modalità di transizione SwipeView scorrere in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla la transizione usata per l'apertura di un `SwipeView`. Viene utilizzato in XAML impostando la proprietà `SwipeView.SwipeTransitionMode` associabile su un valore dell'enumerazione `SwipeTransitionMode`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

Il `SwipeView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il metodo `SwipeView.SetSwipeTransitionMode`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , viene utilizzato per controllare la transizione utilizzata durante l'apertura di un `SwipeView`. L'enumerazione `SwipeTransitionMode` fornisce due valori possibili:

- `Reveal` indica che gli elementi swipe verranno rivelati quando viene visualizzato il contenuto del `SwipeView` e il valore predefinito della proprietà `SwipeView.SwipeTransitionMode`.
- `Drag` indica che gli elementi swipe verranno trascinati nella visualizzazione mentre viene visualizzato il contenuto del `SwipeView`.

Inoltre, è possibile usare il metodo `SwipeView.GetSwipeTransitionMode` per restituire l'`SwipeTransitionMode` applicato al `SwipeView`.

Il risultato è che al `SwipeView`viene applicato un valore di `SwipeTransitionMode` specificato, che controlla la transizione utilizzata per l'apertura del `SwipeView`:

[![Screenshot di SwipeView SwipeTransitionModes, in iOS](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes in iOS")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes in iOS")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
