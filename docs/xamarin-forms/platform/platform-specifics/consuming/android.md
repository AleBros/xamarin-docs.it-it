---
title: Specifiche di piattaforma Android
description: Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche Android che vengono compilate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/17/2017
ms.openlocfilehash: dfc46b5caa14c1c02d1c2afaffecc701fea59e2c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-platform-specifics"></a>Specifiche di piattaforma Android

_Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche Android che vengono compilate in xamarin. Forms._

In Android, xamarin. Forms contiene le specifiche di piattaforma seguenti:

- Impostazione della modalità operativa di una tastiera. Per ulteriori informazioni, vedere [l'impostazione della modalità di Input di tastiera temporanea](#soft_input_mode).
- Abilitare lo scorrimento rapido in un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) per ulteriori informazioni, vedere [abilitare lo scorrimento rapido in un controllo ListView](#fastscroll).
- Abilitazione di passaggio tra le pagine in un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Per ulteriori informazioni, vedere [abilitazione passaggio tra le pagine in un TabbedPage](#enable_swipe_paging).
- Controllare l'ordine Z degli elementi visivi per determinare l'ordine di disegno. Per ulteriori informazioni, vedere [controllo l'elevazione dei privilegi di elementi visivi](#elevation).
- La disabilitazione di [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) e [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) pagina eventi del ciclo di vita in pausa e riprendere, rispettivamente, per le applicazioni che utilizzano delle applicazioni. Per ulteriori informazioni, vedere [la disabilitazione di Disappearing e visualizzati eventi del ciclo di vita della pagina](#disable_lifecycle_events).

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>L'impostazione della modalità di Input di tastiera software

Questo specifico della piattaforma viene utilizzato per impostare la modalità operativa per un'area di input di tastiera e vengono utilizzato in XAML impostando il [ `Application.WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty/) un valore della proprietà associata di [ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) enumerazione:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

Il `Application.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Application.UseWindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) dello spazio dei nomi, viene utilizzato per impostare la modalità operativa area di input di tastiera, con la [ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) fornire due valori di enumerazione: [ `Pan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan/) e [ `Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/). Il `Pan` valore viene utilizzato il [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) opzione rettifica, che non ridimensionarla la finestra quando un controllo di input ha lo stato attivo. Al contrario, il contenuto della finestra Panoramica in modo che lo stato attivo corrente non è nascosto dalla tastiera. Il `Resize` valore viene utilizzato il [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) opzione rettifica che ridimensiona la finestra quando un controllo di input ha lo stato attivo, per liberare spazio per la tastiera.

Il risultato è che la tastiera area modalità operativa può essere impostata quando un controllo di input ha lo stato attivo di input:

[![](android-images/pan-resize.png "Tastiera software specifico della piattaforma modalità operative")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>Abilitare lo scorrimento rapido in un controllo ListView

Questo specifico della piattaforma viene utilizzato per abilitare lo scorrimento rapido attraverso i dati in un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Viene utilizzata in XAML impostando i `ListView.IsFastScrollEnabled` associata a un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

Il `ListView.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il `ListView.SetIsFastScrollEnabled` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) dello spazio dei nomi, viene utilizzato per abilitare lo scorrimento rapido attraverso i dati in un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Inoltre, il `SetIsFastScrollEnabled` metodo può essere utilizzato per attivare o disattivare lo scorrimento rapido chiamando il `IsFastScrollEnabled` per restituire se è abilitato lo scorrimento rapido:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

Il risultato è che lo scorrimento rapido attraverso i dati in un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) può essere abilitato, che modifica la dimensione del cursore di scorrimento:

[![](android-images/fastscroll.png "ListView FastScroll specifico della piattaforma")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>Abilitazione di passaggio tra le pagine in un TabbedPage

Questo specifico della piattaforma viene utilizzato per abilitare passaggio con un movimento dito orizzontale tra le pagine in un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Viene utilizzata in XAML impostando i [ `TabbedPage.IsSwipePagingEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty/) associata a un `boolean` valore:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

Il `TabbedPage.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `TabbedPage.SetIsSwipePagingEnabled` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled/p/Xamarin.Forms.BindableObject/System.Boolean/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) dello spazio dei nomi, viene utilizzato per abilitare passaggio tra le pagine in un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Inoltre, il `TabbedPage` classe il `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` spazio dei nomi contiene inoltre un [ `EnableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/) metodo che consente la specifica della piattaforma, e un [ `DisableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/) metodo che disabilita questo specifico della piattaforma. Il [ `TabbedPage.OffscreenPageLimit` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty/) , proprietà associata e [ `SetOffscreenPageLimit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit/p/Xamarin.Forms.BindableObject/System.Int32/) (metodo), vengono utilizzati per impostare il numero di pagine che deve essere mantenuto in uno stato di inattività su entrambi i lati della pagina corrente.

Il risultato è che paging scorrere le pagine visualizzate da un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) è abilitato:

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>Controllare l'elevazione di elementi visivi

Questo specifico della piattaforma viene usato per controllare l'elevazione dei privilegi o ordine Z degli elementi visivi sulle applicazioni che hanno come destinazione API 21 o maggiore. L'elevazione di un elemento visivo determina l'ordine di disegno, con elementi visivi con valori Z superiori occluding elementi visivi con i valori più bassi Z. Viene utilizzata in XAML impostando i `Elevation.Elevation` associata a un `boolean` valore:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:Elevation.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

Il `Button.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il `Elevation.SetElevation` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) dello spazio dei nomi, viene utilizzato per impostare l'elevazione dell'elemento visivo nullable `float`. Inoltre, il `Elevation.GetElevation` metodo può essere utilizzato per recuperare il valore di elevazione dei privilegi di un elemento visivo.

Il risultato è che l'elevazione di elementi visivi può essere controllato in modo che gli elementi visivi con i valori Z superiore nasconde i colori elementi visivi con i valori più bassi Z. Pertanto, in questo esempio il secondo [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) viene eseguito il rendering di sopra di [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) perché contiene un valore più alto di elevazione dei privilegi:

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>Disabilitare gli scomparsa e vengono visualizzati gli eventi del ciclo di vita di pagina

Questo specifico della piattaforma viene usato per disabilitare il [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) e [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) gli eventi di pagina nell'applicazione sospendere e riprendere, rispettivamente, per le applicazioni che utilizzano delle applicazioni. Inoltre, include la possibilità di controllare se la tastiera viene visualizzata al ripristino, se è stata visualizzata in pausa, a condizione che la modalità operativa della tastiera software è impostata su [ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

> [!NOTE]
> Si noti che questi eventi sono attivati per impostazione predefinita per mantenere il comportamento esistente per le applicazioni che si basano sugli eventi. La disabilitazione di questi eventi rende il ciclo degli eventi delle applicazioni di corrispondere il ciclo di eventi di pre-delle applicazioni.

Questo specifico della piattaforma può essere utilizzato in XAML impostando i [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty/), [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty/), e [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty/) le proprietà associate a `boolean` valori:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

Il `Application.Current.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/) dello spazio dei nomi, è possibile abilitare o disabilitare la generazione di [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) evento della pagina, quando l'applicazione entra in background. Il [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) consente di abilitare o disabilitare la generazione di [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) eventi di pagina, quando l'applicazione riprende lo sfondo. Il [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) metodo viene utilizzato il controllo di visualizzazione la tastiera al ripristino, se visualizzata in pausa, fornito che la modalità operativa della tastiera software è impostata su [ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

Il risultato è che il [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) e [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) gli eventi di pagina non verranno generati in pausa applicazione e riprendere rispettivamente, e la tastiera che, se è stato visualizzato quando l'applicazione è stato sospeso, verrà inoltre visualizzato quando l'applicazione riprende:

[![](android-images/keyboard-on-resume.png "Ciclo di vita eventi specifici per la piattaforma")](android-images/keyboard-on-resume-large.png#lightbox "specifico della piattaforma gli eventi del ciclo di vita")

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare la piattaforma-specifiche Android che vengono compilate in xamarin. Forms. Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
