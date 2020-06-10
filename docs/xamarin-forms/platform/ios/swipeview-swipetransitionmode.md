---
title: "SwipeView scorrere la modalità di transizione su iOS" Descrizione: "le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla la transizione usata per l'apertura di un SwipeView ".
ms. prod: Novell MS. AssetID: C667F24C-BAD8-47E0-9285-D3546BEF703B ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/11/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="swipeview-swipe-transition-mode-on-ios"></a>Modalità di transizione SwipeView scorrere in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla la transizione usata durante l'apertura di un oggetto `SwipeView` . Viene utilizzato in XAML impostando la `SwipeView.SwipeTransitionMode` proprietà associabile su un valore dell' `SwipeTransitionMode` enumerazione:

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

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

Il `SwipeView.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `SwipeView.SetSwipeTransitionMode` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare la transizione utilizzata durante l'apertura di un oggetto `SwipeView` . L' `SwipeTransitionMode` enumerazione fornisce due valori possibili:

- `Reveal`indica che gli elementi swipe verranno rivelati quando il contenuto viene sottomesso a `SwipeView` scorrimento e rappresenta il valore predefinito della `SwipeView.SwipeTransitionMode` Proprietà.
- `Drag`indica che gli elementi swipe verranno trascinati nella visualizzazione mentre viene visualizzato il `SwipeView` contenuto del dito.

Inoltre, il `SwipeView.GetSwipeTransitionMode` metodo può essere utilizzato per restituire l'oggetto `SwipeTransitionMode` applicato a `SwipeView` .

Il risultato è che un `SwipeTransitionMode` valore specificato viene applicato a `SwipeView` , che controlla la transizione utilizzata durante l'apertura di `SwipeView` :

[![Screenshot di SwipeView SwipeTransitionModes, in iOS](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes in iOS")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes in iOS")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
