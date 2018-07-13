---
title: Funzionalità specifiche della piattaforma Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare l'Android-funzionalità specifiche della piattaforma incorporate in xamarin. Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 5ed11e4afb4c061eb7b9dd8f10c67090b4134888
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996386"
---
# <a name="android-platform-specifics"></a>Funzionalità specifiche della piattaforma Android

_Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo viene illustrato come utilizzare l'Android-funzionalità specifiche della piattaforma incorporate in xamarin. Forms._

In Android, xamarin. Forms contiene le funzionalità specifiche della piattaforma seguenti:

- Impostazione della modalità operativa di una tastiera. Per altre informazioni, vedere [impostando la modalità di Input di tastiera temporanea](#soft_input_mode).
- Abilitare lo scorrimento rapido in un [ `ListView` ](xref:Xamarin.Forms.ListView) per altre informazioni, vedere [abilitare lo scorrimento rapido in un ListView](#fastscroll).
- Abilitazione scorrendo rapidamente tra le pagine in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [abilitazione scorrendo rapidamente tra le pagine in un TabbedPage](#enable_swipe_paging).
- Controllare l'ordine Z degli elementi visivi per determinare l'ordine di disegno. Per altre informazioni, vedere [controllare l'elevazione dei privilegi di oggetti visivi](#elevation).
- La disabilitazione il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) pagina degli eventi del ciclo di vita in pausa e riprendere rispettivamente per le applicazioni che usano AppCompat. Per altre informazioni, vedere [disabilitare il Disappearing e visualizzati gli eventi del ciclo di vita di pagina](#disable_lifecycle_events).
- Controllare se un [ `WebView` ](xref:Xamarin.Forms.WebView) può visualizzare il contenuto misto. Per altre informazioni, vedere [abilitazione contenuto misto in una visualizzazione Web](#webview-mixed-content).
- L'impostazione di opzioni dell'editor per la tastiera per il metodo di input un [ `Entry` ](xref:Xamarin.Forms.Entry). Per altre informazioni, vedere [opzioni di impostazione voce Input Method Editor](#entry-imeoptions).
- Disattivazione della modalità colore legacy in supportato [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Per altre informazioni, vedere [disattivazione modalità colore Legacy](#legacy-color-mode).
- Utilizzando la spaziatura interna predefinita e i valori dell'ombreggiatura di pulsanti di Android. Per altre informazioni, vedere [usando i pulsanti Android](#button-padding-shadow).
- Impostare il posizionamento della barra degli strumenti e il colore in una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [posizionamento di impostazione TabbedPage sulla barra degli strumenti e il colore](#tabbedpage-toolbar).

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>Impostazione della modalità di Input di tastiera software

Questo specifico della piattaforma usato per impostare la modalità operativa per un'area di input di tastiera e vengono utilizzato in XAML, impostando il [ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) proprietà associata a un valore del [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumerazione:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

Il `Application.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per impostare la modalità operativa l'area di input di tastiera, con la [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumerazione che fornisce due valori: [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) e [ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize). Il `Pan` valore viene utilizzato il [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) opzione rettifica, che non può essere ridimensionato la finestra quando un controllo di input ha lo stato attivo. Il contenuto della finestra, invece, venga visualizzati in panoramica, in modo che lo stato attivo corrente non è nascosto dalla tastiera. Il `Resize` valore viene utilizzato il [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) opzione rettifica, che si ridimensiona la finestra quando un controllo di input è attivo, per liberare spazio per la tastiera.

Il risultato è che la tastiera area modalità operativa può essere impostata quando un controllo di input ha lo stato attivo di input:

[![](android-images/pan-resize.png "Tastiera funziona in modalità specifiche della piattaforma")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>Abilitare lo scorrimento rapido in un ListView

Questo specifico della piattaforma viene usato per abilitare lo scorrimento rapido di dati in un [ `ListView` ](xref:Xamarin.Forms.ListView). Vengono utilizzati in XAML, impostando il `ListView.IsFastScrollEnabled` proprietà associata una `boolean` valore:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

Il `ListView.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `ListView.SetIsFastScrollEnabled` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per abilitare lo scorrimento rapido di dati in un [ `ListView` ](xref:Xamarin.Forms.ListView). Inoltre, il `SetIsFastScrollEnabled` metodo può essere utilizzato per attivare o disattivare lo scorrimento veloce chiamando il `IsFastScrollEnabled` metodo restituisca se lo scorrimento veloce è abilitato:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

Il risultato è che lo scorrimento veloce tra i dati in un [ `ListView` ](xref:Xamarin.Forms.ListView) può essere abilitata, che modifica la dimensione dello scorrimento:

[![](android-images/fastscroll.png "ListView FastScroll specifiche della piattaforma")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>Abilitazione scorrendo rapidamente tra le pagine in un TabbedPage

Questo specifico della piattaforma viene usato per abilitare scorrendo rapidamente con un movimento del dito orizzontale tra le pagine in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Vengono utilizzati in XAML, impostando il [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) proprietà associata una `boolean` valore:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

Il `TabbedPage.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per abilitare scorrendo rapidamente tra le pagine in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Inoltre, il `TabbedPage` classe nel `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ha anche lo spazio dei nomi un [ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) metodo che consente questo specifico della piattaforma, e una [ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) metodo che consente di disattivare questo specifico della piattaforma. Il [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) proprietà associata, e [ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32)) metodo, vengono usate per impostare il numero di pagine che deve essere mantenuto in uno stato di inattività su entrambi i lati della pagina corrente.

Il risultato è che il paging scorrere rapidamente tramite le pagine visualizzate da un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) è abilitato:

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>Controllo dell'elevazione di elementi visivi

Questo specifico della piattaforma è utilizzato per controllare l'elevazione dei privilegi o ordine Z degli elementi visivi nelle applicazioni che hanno come destinazione API 21 o versione successiva. L'elevazione di un elemento visivo determina l'ordine di disegno, con elementi visivi con i valori più alti di Z occluding elementi visivi con i valori Z più bassi. Vengono utilizzati in XAML, impostando il `VisualElement.Elevation` proprietà associata una `boolean` valore:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

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

Il `Button.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il `VisualElement.SetElevation` metodo, nelle [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene utilizzato per impostare l'elevazione dei privilegi dell'elemento visivo per un valore nullable `float`. Inoltre, il `VisualElement.GetElevation` metodo può essere utilizzato per recuperare il valore di elevazione dei privilegi di un elemento visivo.

Il risultato è che l'elevazione di elementi visivi può essere controllato in modo che gli elementi visivi con i valori più alti Z nasconde gli elementi visivi con i valori più bassi Z i colori. Pertanto, in questo esempio il secondo [ `Button` ](xref:Xamarin.Forms.Button) viene eseguito il rendering sopra il [ `BoxView` ](xref:Xamarin.Forms.BoxView) perché contiene un valore di elevazione dei privilegi più elevato:

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>Disabilitare gli scomparsa e vengono visualizzati gli eventi del ciclo di vita di pagina

Questo specifico della piattaforma viene usato per disabilitare il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventi pagina aplikaci sospendere e riprendere rispettivamente per le applicazioni che usano AppCompat. Inoltre, include la possibilità di controllare se viene visualizzata la tastiera al ripristino, se è stata visualizzata dopo la pausa, condizione che la modalità operativa della tastiera è impostata su [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

> [!NOTE]
> Si noti che questi eventi vengono abilitati per impostazione predefinita per mantenere il comportamento esistente per le applicazioni basate su eventi. La disabilitazione di questi eventi rende il ciclo degli eventi AppCompat corrispondere il ciclo di eventi di pre-AppCompat.

Questo specifico della piattaforma può essere usata in XAML, impostando il [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty), [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty), e [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) proprietà associate a `boolean` valori:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

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

Il `Application.Current.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) dello spazio dei nomi, viene utilizzato per abilitare o disabilitare la generazione dell'evento il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) evento della pagina, quando l'applicazione entra in background. Il [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) metodo viene utilizzato per abilitare o disabilitare la generazione dell'evento il [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) evento pagina, se l'applicazione si riattiva dallo sfondo. Il [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) viene usato il metodo controllo se viene visualizzata la tastiera al ripristino, se è stata visualizzata dopo la pausa, fornite che la modalità operativa della tastiera temporanea è impostata su [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

Il risultato è che il [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) gli eventi della pagina non verranno generati dopo la pausa dell'applicazione e riprendere rispettivamente, e che, se la tastiera era visualizzato quando l'applicazione è stato sospeso, verrà inoltre visualizzato quando l'applicazione riprende a:

[![](android-images/keyboard-on-resume.png "Specifica della piattaforma gli eventi del ciclo di vita")](android-images/keyboard-on-resume-large.png#lightbox "specifiche della piattaforma gli eventi del ciclo di vita")

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>Abilitazione di contenuto misto in una visualizzazione Web

Questo controlli specifici della piattaforma se un [ `WebView` ](xref:Xamarin.Forms.WebView) può Visualizza contenuto misto in applicazioni che hanno come destinazione API 21 o versione successiva. Il contenuto misto pone contenuto che viene caricato inizialmente tramite una connessione HTTPS, ma che carica le risorse (ad esempio immagini, audio, video, fogli di stile, script) su una connessione HTTP. Vengono utilizzati in XAML, impostando il [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) proprietà associata a un valore del [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

Il `WebView.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se il contenuto misto può essere visualizzato, con la [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumerazione che fornisce tre possibili valori:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) consentirà a un'entità origin HTTPS caricare il contenuto da un'origine HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) non consente un'entità origin HTTPS caricare il contenuto da un'origine HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indica che il [ `WebView` ](xref:Xamarin.Forms.WebView) proverà a essere compatibile con l'approccio del web browser del dispositivo più recente. Alcuni contenuti HTTP possono essere autorizzati a essere caricata da un'entità origin HTTPS e altri tipi di contenuto verranno bloccati. I tipi di contenuto che sono bloccati o consentiti possono cambiare a ogni versione del sistema operativo.

Il risultato è che un oggetto specificato [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valore viene applicato per il [ `WebView` ](xref:Xamarin.Forms.WebView), che controlla se è possibile visualizzare il contenuto misto:

[![WebView mista gestione del contenuto specifico della piattaforma](android-images/webview-mixedcontent.png "WebView mista gestione del contenuto specifico della piattaforma")](android-images/webview-mixedcontent-large.png#lightbox "WebView mista specifiche della piattaforma di gestione del contenuto")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>Opzioni di impostazione voce Input Method Editor

Questo specifico della piattaforma imposta il metodo di input opzioni dell'editor (IME) per la tastiera per un [ `Entry` ](xref:Xamarin.Forms.Entry). Ciò include l'impostazione di pulsante di azione utente nell'angolo in basso della tastiera software e le interazioni con di `Entry`. Vengono utilizzati in XAML, impostando il [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) proprietà associata a un valore del [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

Il `Entry.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per impostare l'opzione di azione del metodo di input per la tastiera per il [ `Entry` ](xref:Xamarin.Forms.Entry), con il [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumerazione che fornisce i valori seguenti:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) : indica che è richiesto alcun codice di azione specifico e che il controllo sottostante genererà autonomamente se possibile. Si tratterà `Next` o `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) : indica che nessuna chiave azione sarà disponibile.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) : indica che la chiave dell'azione eseguirà un'operazione di "Vai", che richiede all'utente di destinazione del testo digitato.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) : indica che la chiave dell'azione viene eseguita un'operazione di "ricerca", che richiede all'utente i risultati di ricerca per il testo è stato digitato.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) : indica che la chiave dell'azione eseguirà un'operazione "Invia", fornire il testo per il valore di destinazione.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) : indica che la chiave dell'azione eseguirà un'operazione di "next", indirizzare l'utente al campo successivo in grado di accettare testo.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) : indica che la chiave dell'azione eseguirà un'operazione di "completata", chiudere la tastiera.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) : indica che la chiave dell'azione eseguirà un'operazione "precedente", indirizzare l'utente al campo precedente in grado di accettare testo.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – la maschera per selezionare le opzioni di azione.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) : indica che il controllo ortografico sarà informazioni da parte dell'utente, né suggeriscono correzioni basate su ciò che l'utente ha digitato in precedenza.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) : indica che l'interfaccia utente non va a schermo intero.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) : indica che nessuna interfaccia utente verrà visualizzato per il testo estratto.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) : indica che non verrà visualizzata alcuna interfaccia utente per le azioni personalizzate.

Il risultato è che un oggetto specificato [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valore viene applicato per la tastiera per il [ `Entry` ](xref:Xamarin.Forms.Entry), il metodo di input che imposta le opzioni dell'editor:

[![Metodo editor specifici della piattaforma di input voce](android-images/entry-imeoptions.png "movimento di input metodo editor specifici della piattaforma")](android-images/entry-imeoptions-large.png#lightbox "movimento di input specifico della piattaforma editor (metodo)")

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>La disabilitazione della modalità di colore precedente

Alcune delle viste di xamarin. Forms offrono una modalità colore legacy. In questa modalità, quando la [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) della visualizzazione è impostata su `false`, la visualizzazione sostituiranno i colori impostati dall'utente con i colori nativo predefinito per lo stato disabilitato. Per garantire la compatibilità, questa modalità legacy colore rimane con le versioni precedenti il comportamento predefinito per le visualizzazioni supportate.

Questo specifico della piattaforma disabilita questa modalità colore legacy, in modo che i colori impostati su una vista dall'utente rimangano anche quando la visualizzazione è disabilitata. Vengono utilizzati in XAML, impostando il [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) proprietà associata `false`:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

Il `VisualElement.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usata per controllare se la modalità legacy è disabilitata. Inoltre, il [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) metodo può essere utilizzato da restituire se la modalità legacy è disabilitata.

Il risultato è che può essere disabilitata la modalità legacy, in modo che rimangano anche i colori impostati su una vista dall'utente quando la visualizzazione è disabilitata:

![](android-images/legacy-color-mode-disabled.png "Modalità legacy colore disabilitata")

> [!NOTE]
> Quando si imposta una [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) in una vista, la modalità legacy di colori viene completamente ignorata. Per altre informazioni sugli stati visual, vedere [di xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="button-padding-shadow" />

## <a name="using-android-buttons"></a>Mediante pulsanti di Android

Questo specifico della piattaforma controlla se i pulsanti di xamarin. Forms usano la spaziatura interna predefinita e i valori di ombreggiatura di pulsanti di Android. Vengono utilizzati in XAML, impostando il [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) proprietà connesse alla `boolean` valori:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

Il `Button.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) e[ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) metodi, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, usate per controllare se il valore predefinito di usare i pulsanti di xamarin. Forms spaziatura interna e i valori dell'ombreggiatura di pulsanti di Android. Inoltre, il [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) metodi possono essere utilizzati da restituire se un pulsante utilizza il valore predefinito di riempimento valore e il valore predefinito dell'ombreggiatura, rispettivamente.

Il risultato è che i pulsanti di xamarin. Forms è possono usare la spaziatura interna predefinita e i valori di ombreggiatura di pulsanti di Android:

![](android-images/button-padding-and-shadow.png "Modalità legacy colore disabilitata")

Si noti che nella schermata sopra ogni [ `Button` ](xref:Xamarin.Forms.Button) ha definizioni identiche, tranne il fatto che a destra `Button` Usa la spaziatura interna predefinita e i valori di ombreggiatura di pulsanti di Android.

<a name="tabbedpage-toolbar" />

## <a name="setting-tabbedpage-toolbar-placement-and-color"></a>Colore e la posizione della barra degli strumenti TabbedPage impostazione

Queste funzionalità specifiche della piattaforma vengono usate per impostare il posizionamento e il colore della barra degli strumenti in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Vengono utilizzati in XAML, impostando il [ `TabbedPage.ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.toolbarplacementproperty?view=xamarin-forms) proprietà associata a un valore del [ `ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement?view=xamarin-forms) enumerazione e il [ `TabbedPage.BarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.baritemcolorproperty?view=xamarin-forms) e [ `TabbedPage.BarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.barselecteditemcolorproperty?view=xamarin-forms) associate a un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

In alternativa, può essere usati da c# usando l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

Il `TabbedPage.On<Android>` metodo consente di specificare che queste funzionalità specifiche della piattaforma verranno eseguito solo in Android. Il [ `TabbedPage.SetToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.settoolbarplacement?view=xamarin-forms) (metodo), nella [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, viene usato per impostare la posizione della barra degli strumenti in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), con la [ `ToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement?view=xamarin-forms) enumerazione che fornisce i valori seguenti:

- [`Default`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Default) : indica che la barra degli strumenti viene posizionato in corrispondenza della posizione predefinita della pagina. Questa è la parte superiore della pagina nei telefoni e la parte inferiore della pagina in altri idiomi di dispositivo.
- [`Top`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Top) : indica che la barra degli strumenti viene posizionata nella parte superiore della pagina.
- [`Bottom`](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.toolbarplacement#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_ToolbarPlacement_Bottom) : indica che la barra degli strumenti viene posizionata nella parte inferiore della pagina.

Inoltre, il [ `TabbedPage.SetBarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.setbaritemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_SetBarItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__Xamarin_Forms_Color_) e [ `TabbedPage.SetBarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.setbarselecteditemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_SetBarSelectedItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__Xamarin_Forms_Color_) metodi vengono usati per impostare il colore degli elementi della barra degli strumenti e gli elementi della barra selezionata, rispettivamente.

> [!NOTE]
> Il [ `GetToolbarPlacement` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.gettoolbarplacement?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetToolbarPlacement_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__), [ `GetBarItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.getbaritemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetBarItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__), e [ `GetBarSelectedItemColor` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.platformconfiguration.androidspecific.tabbedpage.getbarselecteditemcolor?view=xamarin-forms#Xamarin_Forms_PlatformConfiguration_AndroidSpecific_TabbedPage_GetBarSelectedItemColor_Xamarin_Forms_IPlatformElementConfiguration_Xamarin_Forms_PlatformConfiguration_Android_Xamarin_Forms_TabbedPage__) metodi possono essere utilizzati per recuperare il posizionamento e il colore del [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) sulla barra degli strumenti.

Il risultato è che la posizione della barra degli strumenti, il colore degli elementi della barra degli strumenti e il colore dell'elemento selezionato della barra degli strumenti può essere impostate su un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](android-images/tabbedpage-toolbar-placement.png)

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare l'Android-funzionalità specifiche della piattaforma incorporate in xamarin. Forms. Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
