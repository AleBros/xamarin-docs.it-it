---
title: Classe di dispositivi di xamarin. Forms
description: Questo articolo illustra come usare la classe di dispositivi di xamarin. Forms, per un controllo accurato sul layout e funzionalità in base a ogni piattaforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2018
ms.openlocfilehash: 4ba4bd7528b635d099868f093268d2d83e44dae0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61359882"
---
# <a name="xamarinforms-device-class"></a>Classe di dispositivi di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)

Il [ `Device` ](xref:Xamarin.Forms.Device) classe contiene un numero di proprietà e metodi che consentono agli sviluppatori di personalizzare i layout e le funzionalità in base a ogni piattaforma.

Oltre ai metodi e proprietà per il codice di destinazione in tipi di hardware specifico e dimensioni, il `Device` classe include le [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) metodo che deve essere utilizzato durante l'interazione con l'interfaccia utente dei controlli da thread in background.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Fornendo valori specifici della piattaforma

Prima di xamarin. Forms 2.3.4, la piattaforma su cui era in esecuzione l'applicazione è stato possibile ottenere esaminando il [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) proprietà e confrontandola con la [ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS), [ `TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android), [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone), e [ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows) valori di enumerazione. Analogamente, uno dei [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) overload può essere utilizzato per fornire valori specifici della piattaforma da un controllo.

Tuttavia, poiché xamarin. Forms 2.3.4 queste API sono state deprecate e sostituite. Il [ `Device` ](xref:Xamarin.Forms.Device) classe ora contiene le costanti di stringa pubblica che identificano le piattaforme – [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS), [ `Device.Android` ](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`( deprecato), `Device.WinRT` (deprecata), [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP), e [ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS). Analogamente, il [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) overload sono stati sostituiti con il [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) e [ `On` ](xref:Xamarin.Forms.On) API.

In c#, è possibile fornire valori specifici della piattaforma tramite la creazione di un `switch` struzione il [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) proprietà e quindi fornendo `case` istruzioni per le piattaforme necessarie:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

Il [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) e [ `On` ](xref:Xamarin.Forms.On) classi forniscono la stessa funzionalità in XAML:

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

Il [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) classe è una classe generica che deve essere creata un'istanza con un `x:TypeArguments` attributo che corrisponde al tipo di destinazione. Nel [ `On` ](xref:Xamarin.Forms.On) (classe), il [ `Platform` ](xref:Xamarin.Forms.On.Platform) attributo può accettare un singolo `string` valore o più valori delimitati da virgole `string` valori.

> [!IMPORTANT]
> Che fornisce un'implementazione non corretta `Platform` nel valore dell'attributo di `On` classe non comporterà un errore. Al contrario, il codice verrà eseguito senza il valore specifico della piattaforma in corso l'applicazione.

In alternativa, il `OnPlatform` estensione di markup può essere utilizzato in XAML per personalizzare l'aspetto dell'interfaccia utente in base a ogni piattaforma. Per altre informazioni, vedere [estensione di Markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

Il `Device.Idiom` proprietà può essere utilizzata per modificare layout o la funzionalità a seconda del dispositivo, l'applicazione è in esecuzione. Il [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom) enumerazione contiene i valori seguenti:

-  **Telefono** : iPhone, iPod touch e i dispositivi Android più stretto di 600 DIP ^
-  **Tablet** : iPad, i dispositivi Windows e i dispositivi Android più ampio di 600 DIP ^
-  **Desktop** : solo restituito nella [App della piattaforma UWP](~/xamarin-forms/platform/windows/installation/index.md) nei computer desktop Windows 10 (restituisce `Phone` nei dispositivi mobili di Windows, inclusi negli scenari di continuità)
-  **TV** – dispositivi Tizen TV
-  **Espressioni di controllo** – dispositivi watch Tizen
-  **Non supportato** : non usato

*^ DIP non è necessariamente il numero di pixel fisici*

Il `Idiom` proprietà risulta particolarmente utile per la creazione di layout di sfruttare i vantaggi di schermi di dimensioni maggiori, simile al seguente:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

Il [ `OnIdiom` ](xref:Xamarin.Forms.OnIdiom`1) classe fornisce la stessa funzionalità in XAML:

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

Il [ `OnIdiom` ](xref:Xamarin.Forms.OnPlatform`1) classe è una classe generica che deve essere creata un'istanza con un `x:TypeArguments` attributo che corrisponde al tipo di destinazione.

In alternativa, il `OnIdiom` estensione di markup è utilizzabile in XAML per personalizzare l'aspetto dell'interfaccia utente in base l'idioma del dispositivo su cui è in esecuzione l'applicazione. Per altre informazioni, vedere [estensione di Markup OnIdiom](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device.FlowDirection

Il [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valore recupera una [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) valore di enumerazione che rappresenta la direzione del flusso corrente utilizzata dal dispositivo. La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. I valori dell'enumerazione sono:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

In XAML, il [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) valore può essere recuperato tramite il `x:Static` estensione di markup:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Il codice nel linguaggio c# equivalente è:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

Il [ `Styles` proprietà](~/xamarin-forms/user-interface/styles/index.md) contiene le definizioni di stile incorporato che possono essere applicate a alcuni controlli (ad esempio `Label`) `Style` proprietà. Gli stili disponibili sono:

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` può essere usato per impostare [ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) nel codice c#:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

<a name="Device_OpenUri" />

## <a name="deviceopenuri"></a>Device.OpenUri

Il `OpenUri` metodo può essere utilizzato per attivare operazioni nella piattaforma sottostante, ad esempio aprire un URL nel browser web nativi (**Safari** in iOS o **Internet** in Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

Il [esempio di WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) include un esempio di utilizzo `OpenUri` per aprire gli URL e anche attivare le chiamate telefoniche.

Il [esempio di mappe](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) Usa anche `Device.OpenUri` per visualizzare le mappe e le direzioni utilizzando l'oggetto nativo **esegue il mapping** App in iOS e Android.

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

Il `Device` classe include anche un `StartTimer` metodo che fornisce un modo semplice per attivare le attività dipendenti dal tempo utilizzabile nel codice comune con xamarin. Forms, inclusa una libreria .NET Standard. Passare un `TimeSpan` per impostare l'intervallo e restituire `true` mantenere il timer in esecuzione o `false` interromperlo dopo la chiamata corrente.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se il codice all'interno del timer interagisce con l'interfaccia utente (ad esempio l'impostazione del testo di un `Label` oppure che venga visualizzato un avviso) deve essere eseguita all'interno di un `BeginInvokeOnMainThread` espressione (vedere sotto).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Elementi dell'interfaccia utente non devono mai essere accessibile tramite i thread in background, ad esempio il codice in esecuzione in un timer o un gestore di completamento per le operazioni asincrone, ad esempio richieste web. Qualsiasi codice in background che è necessario aggiornare l'interfaccia utente debba essere incapsulato all'interno [ `BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)). Questo è l'equivalente di `InvokeOnMainThread` in iOS `RunOnUiThread` in Android, e `Dispatcher.RunAsync` sulla piattaforma Windows universale.

Il codice xamarin. Forms è:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Nota che i metodi utilizzando `async/await` non è necessario usare `BeginInvokeOnMainThread` se vengono eseguiti dal thread principale dell'interfaccia utente.

## <a name="summary"></a>Riepilogo

Xamarin. Forms `Device` classe consente un controllo con granularità fine sulle funzionalità e i layout in base a ogni piattaforma: in comune anche il codice (progetti di libreria .NET Standard o progetti condivisi).


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di dispositivo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Esempio di stili](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dispositivo](xref:Xamarin.Forms.Device)
