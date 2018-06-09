---
title: Classe di dispositivi di xamarin. Forms
description: In questo articolo viene illustrato come utilizzare la classe di dispositivi di xamarin. Forms, per un controllo accurato funzionalità e i layout di base per ogni piattaforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: cdcd4bb08b9dac84d264f7c0c41e16d68380e1d0
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242544"
---
# <a name="xamarinforms-device-class"></a>Classe di dispositivi di xamarin. Forms

Il [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe contiene un numero di proprietà e metodi che consentono agli sviluppatori di personalizzare il layout e funzionalità di base per ogni piattaforma.

Oltre ai metodi e proprietà per il codice di destinazione in tipi di hardware specifici e le dimensioni, il `Device` classe include il [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) metodo che deve essere utilizzato quando l'interazione con l'interfaccia utente dei controlli thread in background.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Inserimento di valori specifici della piattaforma

Prima di xamarin. Forms 2.3.4, la piattaforma su cui era in esecuzione l'applicazione può essere ottenuta mediante l'esame di [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) proprietà e il confronto per il [ `TargetPlatform.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/), [ `TargetPlatform.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/), [ `TargetPlatform.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), e [ `TargetPlatform.Windows` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) valori di enumerazione. Analogamente, uno del [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) overload può essere usati per specificare i valori specifici della piattaforma di un controllo.

Tuttavia, poiché xamarin. Forms 2.3.4 queste API sono state deprecate e sostituite. Il [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe ora contiene le costanti di stringa pubblica che identificano le piattaforme – [ `Device.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), [ `Device.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), [ `Device.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/) (deprecata), [ `Device.WinRT` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinRT/) (obsoleto), [ `Device.UWP` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), e [ `Device.macOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.macOS/). Analogamente, il [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) overload sono stati sostituiti con il [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) e [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) API.

In c#, è possono specificare i valori specifici della piattaforma creando un `switch` istruzione il [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) proprietà e quindi fornendo `case` istruzioni per la piattaforma richiesta:

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

Il [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) e [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) classi forniscono la stessa funzionalità in XAML:

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

Il [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) classe è una classe generica e pertanto deve essere creata con un `x:TypeArguments` attributo che corrisponde al tipo di destinazione. Nel [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) (classe), il [ `Platform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.On.Platform/) attributo può accettare un singolo `string` valore o più delimitato da virgole `string` valori.

> [!IMPORTANT]
> Fornisce un'implementazione non corretta `Platform` valore nell'attributo di `On` classe non verrà generato un errore. Al contrario, il codice viene eseguito senza il valore specifico della piattaforma viene applicato.

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

Il `Device.Idiom` possono essere utilizzati per modificare i layout o la funzionalità a seconda del dispositivo è in esecuzione l'applicazione. Il [ `TargetIdiom` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetIdiom/) enumerazione contiene i valori seguenti:

-  **Telefono** – iPhone, iPod touch e i dispositivi Android inferiore a 600 DIP ^
-  **Tablet** : iPad, i dispositivi Windows e i dispositivi Android più ampie di quelle 600 DIP ^
-  **Desktop** : solo restituito in [App UWP](~/xamarin-forms/platform/windows/installation/index.md) nei computer desktop Windows 10 (restituisce `Phone` nei dispositivi mobili di Windows, inclusi negli scenari di continuità)
-  **TV** – dispositivi Tizen TV
-  **Non supportato** : inutilizzati

*^ DIP non è necessariamente il numero di pixel fisici*

`Idiom` è particolarmente utile per la creazione di layout che sfruttano schermi di dimensioni maggiori, simile al seguente:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

## <a name="deviceflowdirection"></a>Device.FlowDirection

Il [ `Device.FlowDirection` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.FlowDirection/) valore recupera un [ `FlowDirection` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FlowDirection/) valore di enumerazione che rappresenta la direzione del flusso corrente in uso dal dispositivo. Direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio. I valori dell'enumerazione sono:

- [`LeftToRight`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.LeftToRight/)
- [`RightToRight`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.RightToLeft/)
- [`MatchParent`](https://developer.xamarin.com/api/field/Xamarin.Forms.FlowDirection.MatchParent/)

In XAML, il [ `Device.FlowDirection` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.FlowDirection/) valore può essere recuperato tramite il `x:Static` estensione di markup:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

È l'equivalente del codice in c#:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Per ulteriori informazioni sulla direzione di flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

Il [ `Styles` proprietà](~/xamarin-forms/user-interface/styles/index.md) contiene le definizioni di stile predefinito che possono essere applicate a alcuni controlli (ad esempio `Label`) `Style` proprietà. Gli stili disponibili sono:

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` può essere utilizzato durante l'impostazione [ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) nel codice c#:

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

Il `OpenUri` metodo può essere utilizzato per attivare operazioni sulla piattaforma sottostante, ad esempio aprire un URL nel browser web nativi (**Safari** in iOS o **Internet** in Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

Il [esempio WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) include un esempio di utilizzo `OpenUri` per aprire gli URL e inoltre generare chiamate telefoniche.

Il [esempio mappe](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) utilizza inoltre `Device.OpenUri` per visualizzare mappe e indicazioni utilizzando nativo **esegue il mapping** App in iOS e Android.

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

Il `Device` classe include inoltre un `StartTimer` metodo che fornisce un modo semplice per attivare le attività dipendenti dal tempo che viene utilizzato codice comune con xamarin. Forms, inclusa una libreria .NET Standard. Passare un `TimeSpan` per impostare l'intervallo e restituire `true` per mantenere il timer in esecuzione o `false` per interromperla, dopo la chiamata corrente.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se il codice all'interno del timer interagisce con l'interfaccia utente (ad esempio l'impostazione del testo di un `Label` oppure che venga visualizzato un avviso) deve essere eseguita all'interno di un `BeginInvokeOnMainThread` espressione (vedere sotto).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Elementi dell'interfaccia utente non devono essere accessibile tramite i thread in background, ad esempio di codice in esecuzione in un timer o un gestore di completamento per le operazioni asincrone, ad esempio richieste web. Qualsiasi codice in background che è necessario aggiornare l'interfaccia utente deve essere racchiusa all'interno di [ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/). Questo è l'equivalente di `InvokeOnMainThread` in iOS `RunOnUiThread` in Android, e `Dispatcher.RunAsync` nella piattaforma Windows universale.

Il codice di xamarin. Forms è:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Si noti che tramite i metodi `async/await` non è necessario utilizzare `BeginInvokeOnMainThread` se vengono eseguiti dal thread principale dell'interfaccia utente.

## <a name="summary"></a>Riepilogo

Il xamarin. Forms `Device` classe consente a un controllo accurato funzionalità e i layout di base per ogni piattaforma, anche in comune codice (progetti di libreria .NET Standard o i progetti condivisi).


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di dispositivo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Esempio di stili](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dispositivo](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)
