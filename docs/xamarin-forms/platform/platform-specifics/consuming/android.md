---
title: Specifiche di piattaforma Android
description: Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche Android che vengono compilate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 05f1fc6158e9a20892ab4a4b49b33e4eac6bc5e5
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733061"
---
# <a name="android-platform-specifics"></a>Specifiche di piattaforma Android

_Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti. In questo articolo viene illustrato come utilizzare la piattaforma-specifiche Android che vengono compilate in xamarin. Forms._

In Android, xamarin. Forms contiene le specifiche di piattaforma seguenti:

- Impostazione della modalità operativa di una tastiera. Per ulteriori informazioni, vedere [l'impostazione della modalità di Input di tastiera temporanea](#soft_input_mode).
- Abilitare lo scorrimento rapido in un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) per ulteriori informazioni, vedere [abilitare lo scorrimento rapido in un controllo ListView](#fastscroll).
- Abilitazione di passaggio tra le pagine in un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Per ulteriori informazioni, vedere [abilitazione passaggio tra le pagine in un TabbedPage](#enable_swipe_paging).
- Controllare l'ordine Z degli elementi visivi per determinare l'ordine di disegno. Per ulteriori informazioni, vedere [controllo l'elevazione dei privilegi di elementi visivi](#elevation).
- La disabilitazione di [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) e [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) pagina eventi del ciclo di vita in pausa e riprendere, rispettivamente, per le applicazioni che utilizzano delle applicazioni. Per ulteriori informazioni, vedere [la disabilitazione di Disappearing e visualizzati eventi del ciclo di vita della pagina](#disable_lifecycle_events).
- Controllare se un [ `WebView` ](xref:Xamarin.Forms.WebView) possibile visualizzare il contenuto misto. Per altre informazioni, vedere [abilitazione contenuto misto in un WebView](#webview-mixed-content).
- L'impostazione di opzioni dell'editor per la tastiera su schermo per il metodo di input un [ `Entry` ](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [opzioni di impostazione voce Input Method Editor](#entry-imeoptions).
- Disabilitare la modalità legacy di colore in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [la disabilitazione di colore Legacy](#legacy-color-mode).
- Utilizza i valori di ombreggiatura dei pulsanti Android e riempimento predefinito. Per altre informazioni, vedere [mediante pulsanti di Android](#button-padding-shadow).

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

Questo specifico della piattaforma viene usato per controllare l'elevazione dei privilegi o ordine Z degli elementi visivi sulle applicazioni che hanno come destinazione API 21 o maggiore. L'elevazione di un elemento visivo determina l'ordine di disegno, con elementi visivi con valori Z superiori occluding elementi visivi con i valori più bassi Z. Viene utilizzata in XAML impostando i `VisualElement.Elevation` associata a un `boolean` valore:

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
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
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

Il `Button.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il `VisualElement.SetElevation` (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) dello spazio dei nomi, viene utilizzato per impostare l'elevazione dell'elemento visivo nullable `float`. Inoltre, il `VisualElement.GetElevation` metodo può essere utilizzato per recuperare il valore di elevazione dei privilegi di un elemento visivo.

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

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>L'abilitazione di contenuto misto in un WebView

Questo controlli specifici della piattaforma se un [ `WebView` ](xref:Xamarin.Forms.WebView) possibile Visualizza contenuto misto nelle applicazioni destinate a API 21 o versione successiva. Contenuto misto è che viene caricato inizialmente tramite una connessione HTTPS, ma che vengono caricate le risorse (ad esempio immagini, audio, video, fogli di stile, script) su una connessione HTTP. Viene utilizzata in XAML impostando il [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) un valore della proprietà associata di [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

Il `WebView.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene utilizzato per controllare se è possibile visualizzare il contenuto misto, con il [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione che fornisce tre possibili valori:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) consentirà un'entità origin HTTPS caricare il contenuto da un'origine HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) non consente un'entità origin HTTPS caricare il contenuto da un'origine HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) tenterà di essere compatibile con l'approccio del browser web dispositivo più recente. Parte del contenuto HTTP potrà essere caricata da un'entità origin HTTPS e altri tipi di contenuto verranno bloccate. I tipi di contenuto che sono bloccati o consentiti possono variare a ogni versione del sistema operativo.

Il risultato è che un determinato [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valore si riferisce al [ `WebView` ](xref:Xamarin.Forms.WebView), che controlla se è possibile visualizzare contenuto misto:

[![WebView mista specifico della piattaforma di gestione del contenuto](android-images/webview-mixedcontent.png "WebView mista specifico della piattaforma di gestione del contenuto")](android-images/webview-mixedcontent-large.png#lightbox "WebView mista specifico della piattaforma di gestione del contenuto")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>Opzioni di impostazione voce Input Method Editor

Questo specifico della piattaforma imposta il metodo di input opzioni dell'editor (IME) per la tastiera su schermo per un [ `Entry` ](xref:Xamarin.Forms.Entry). Ciò include l'impostazione di pulsante di azione utente nell'angolo in basso della tastiera su schermo e le interazioni con il `Entry`. Viene utilizzata in XAML impostando il [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) un valore della proprietà associata di [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

Il `Entry.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) (metodo), nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene utilizzato per impostare l'opzione di azione del metodo di input per la tastiera su schermo per i [ `Entry` ](xref:Xamarin.Forms.Entry), con il [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione che fornisce i valori seguenti:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indica che non è necessaria alcuna azione specifica chiave e che il controllo sottostante produrrà autonomamente se possibile. Si tratterà `Next` o `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indica che non chiave dell'azione verrà resa disponibile.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) : indica che la chiave dell'azione eseguirà un'operazione "go", che richiede all'utente di destinazione del testo digitato.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) : indica che la chiave dell'azione esegue un'operazione di "ricerca", che richiede all'utente i risultati di ricerca per il testo digitati.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) : indica che la chiave dell'azione eseguirà un'operazione "send", fornire il testo alla relativa destinazione.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) : indica che la chiave dell'azione eseguirà un'operazione "Avanti", richiede all'utente nel campo successivo che verrà accetta un testo.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) : indica che la chiave dell'azione eseguirà un'operazione "done", chiudendo la tastiera su schermo.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) : indica che la chiave dell'azione eseguirà un'operazione "precedente", richiede all'utente al campo precedente che accetterà testo.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – la maschera per selezionare le opzioni di azione.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) : indica che il controllo ortografico sarà informazioni da parte dell'utente, né Suggerisci correzioni in base a ciò che l'utente ha digitato in precedenza.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) : indica che l'interfaccia utente non va a schermo intero.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indica che è verrà visualizzata alcuna interfaccia utente per testo estratto.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indica che non verrà visualizzata alcuna interfaccia utente per le azioni personalizzate.

Il risultato è che un determinato [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valore viene applicato per la tastiera su schermo per il [ `Entry` ](xref:Xamarin.Forms.Entry), che imposta il metodo di input opzioni dell'editor:

[![Metodo editor specifico per la piattaforma di input voce](android-images/entry-imeoptions.png "voce metodo editor specifico per la piattaforma di input")](android-images/entry-imeoptions-large.png#lightbox "voce di input specifico della piattaforma editor (metodo)")

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Disabilitare la modalità colore Legacy

Alcune delle viste di xamarin. Forms offrono una modalità colore legacy. In questa modalità, quando il [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) della visualizzazione è impostata su `false`, la vista sostituirà i colori impostati dall'utente con i colori predefiniti nativi per lo stato disabilitato. Per garantire la compatibilità, questa modalità colore legacy rimarrà con le versioni precedenti il comportamento predefinito per le visualizzazioni supportate.

Questo specifico della piattaforma disattiva questa modalità colore legacy, in modo che i colori impostato su una vista dall'utente rimangano anche quando la visualizzazione è disabilitata. Viene utilizzata in XAML impostando i [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) associata a `false`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se la modalità legacy è disabilitata. Inoltre, il [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) metodo può essere utilizzato per restituire se la modalità legacy è disabilitata.

Il risultato è che è possibile disabilitare la modalità legacy di colore, in modo che rimangano anche colori impostato su una vista dall'utente quando viene disattivata la vista:

![](android-images/legacy-color-mode-disabled.png "Modalità legacy colore disabilitata")

> [!NOTE]
> Durante l'impostazione di un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) su una vista, la modalità legacy colore viene ignorata completamente. Per ulteriori informazioni sugli stati visivi, vedere [il gestore degli stati Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="button-padding-shadow" />

## <a name="using-android-buttons"></a>Utilizzo di pulsanti Android

Questo specifico della piattaforma controlla se i pulsanti di xamarin. Forms utilizzano il riempimento predefinito e i valori di ombreggiatura dei pulsanti Android. Viene utilizzata in XAML impostando i [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) associate a `boolean` valori:

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

Il `Button.On<Android>` metodo specifica che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) e[ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) metodi nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, vengono utilizzati per controllare se i pulsanti di xamarin. Forms Usa il valore predefinito spaziatura interna e i valori di ombreggiatura dei pulsanti Android. Inoltre, il [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) metodi possono essere utilizzati da restituire se un pulsante utilizza il valore predefinito padding valore e il valore di ombreggiatura predefinito, rispettivamente.

Il risultato è che i pulsanti di xamarin. Forms possono usare il riempimento predefinito e i valori di ombreggiatura dei pulsanti Android:

![](android-images/button-padding-and-shadow.png "Modalità legacy colore disabilitata")

Si noti che nella cattura di schermata sopra ciascuno [ `Button` ](xref:Xamarin.Forms.Button) con definizioni identiche, tranne il fatto che il destro `Button` utilizza il riempimento predefinito e i valori di ombreggiatura dei pulsanti Android.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare la piattaforma-specifiche Android che vengono compilate in xamarin. Forms. Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare un renderer personalizzato o effetti.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
