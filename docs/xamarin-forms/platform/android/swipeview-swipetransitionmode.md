---
title: Modalità di transizione SwipeView scorrere in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. In questo articolo viene illustrato come utilizzare la piattaforma Android specifica che controlla la transizione utilizzata durante l'apertura di un SwipeView.
ms.prod: xamarin
ms.assetid: 6B1F8213-9D62-4C40-9F04-881F1371B5AA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 077d4a8a9530bf074fde710dd08c1fbea49668ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490353"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>Modalità di transizione SwipeView scorrere in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma Android controlla la transizione usata per l'apertura di un `SwipeView`. Viene utilizzato in XAML impostando la proprietà `SwipeView.SwipeTransitionMode` associabile su un valore dell'enumerazione `SwipeTransitionMode`:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

Il `SwipeView.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il metodo `SwipeView.SetSwipeTransitionMode`, nello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , viene utilizzato per controllare la transizione utilizzata durante l'apertura di un `SwipeView`. L'enumerazione `SwipeTransitionMode` fornisce due valori possibili:

- `Reveal` indica che gli elementi swipe verranno rivelati quando viene visualizzato il contenuto del `SwipeView` e il valore predefinito della proprietà `SwipeView.SwipeTransitionMode`.
- `Drag` indica che gli elementi swipe verranno trascinati nella visualizzazione mentre viene visualizzato il contenuto del `SwipeView`.

Inoltre, è possibile usare il metodo `SwipeView.GetSwipeTransitionMode` per restituire l'`SwipeTransitionMode` applicato al `SwipeView`.

Il risultato è che al `SwipeView`viene applicato un valore di `SwipeTransitionMode` specificato, che controlla la transizione utilizzata per l'apertura del `SwipeView`:

[![Screenshot di SwipeView SwipeTransitionModes, in Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes in Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes in Android")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
