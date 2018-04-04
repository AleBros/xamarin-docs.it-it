---
title: Classe di dispositivi
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 7ba3808e7b8d948d502be3f80b8830e1aaf3b52f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="device-class"></a>Classe di dispositivi

Il [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe contiene un numero di proprietà e metodi che consentono agli sviluppatori di personalizzare il layout e funzionalità di base per ogni piattaforma.

Oltre ai metodi e proprietà per il codice di destinazione in tipi di hardware specifici e le dimensioni, il `Device` classe include il [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) metodo che deve essere utilizzato quando l'interazione con l'interfaccia utente dei controlli thread in background.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Inserimento di valori specifici della piattaforma

Prima di xamarin. Forms 2.3.4, la piattaforma su cui era in esecuzione l'applicazione può essere ottenuta mediante l'esame di [ `Device.OS` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.OS/) proprietà e il confronto per il [ `TargetPlatform.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.iOS/), [ `TargetPlatform.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Android/), [ `TargetPlatform.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.WinPhone/), e [ `TargetPlatform.Windows` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TargetPlatform.Windows/) valori di enumerazione. Analogamente, uno del [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) overload può essere usati per specificare i valori specifici della piattaforma di un controllo.

Tuttavia, poiché xamarin. Forms 2.3.4 queste API sono state deprecate e sostituite. Il [ `Device` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/) classe ora contiene le costanti stringa pubblica che identificano le piattaforme – [ `Device.iOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.iOS/), [ `Device.Android` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.Android/), [ `Device.WinPhone` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinPhone/), [ `Device.WinRT` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.WinRT/), [ `Device.UWP` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.UWP/), e [ `Device.macOS` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Device.macOS/). Analogamente, il [ `Device.OnPlatform` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.OnPlatform/p/System.Action/System.Action/System.Action/System.Action/) overload sono stati sostituiti con il [ `OnPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.OnPlatform%3CT%3E/) e [ `On` ](https://developer.xamarin.com/api/type/Xamarin.Forms.On/) API.

In c#, è possono specificare i valori specifici della piattaforma creando un `switch` istruzione il [ `Device.RuntimePlatform` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Device.RuntimePlatform/) proprietà e quindi fornendo `case` istruzioni per la piattaforma richiesta:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.WinPhone:
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
      <On Platform="Android, WinPhone, UWP" Value="0,0,0,0" />
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

-  **Telefono** – iPhone, iPod touch di Windows Phone, Android dispositivi inferiore a 600 DIP ^
-  **Tablet** : iPad, i computer Windows 8.1, i dispositivi Android più ampio di 600 DIP ^
-  **Desktop** : solo restituito in [App UWP](~/xamarin-forms/platform/windows/installation/universal.md) nei computer desktop Windows 10 (restituisce `Phone` nei dispositivi mobili di Windows, inclusi negli scenari di continuità)
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

Il `Device` classe include inoltre un `StartTimer` metodo che fornisce un modo semplice per attivare le attività dipendenti dal tempo utilizzabile nel codice comune di xamarin. Forms (inclusi PCLs). Passare un `TimeSpan` per impostare l'intervallo e restituire `true` per mantenere il timer in esecuzione o `false` per interromperla, dopo la chiamata corrente.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se il codice all'interno del timer interagisce con l'interfaccia utente (ad esempio l'impostazione del testo di un `Label` oppure che venga visualizzato un avviso) deve essere eseguita all'interno di un `BeginInvokeOnMainThread` espressione (vedere sotto).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Elementi dell'interfaccia utente non devono essere accessibile tramite i thread in background, ad esempio di codice in esecuzione in un timer o un gestore di completamento per le operazioni asincrone, ad esempio richieste web. Qualsiasi codice in background che è necessario aggiornare l'interfaccia utente deve essere racchiusa all'interno di [ `BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/). Questo è l'equivalente di `InvokeOnMainThread` in iOS, `RunOnUiThread` in Android, e `Dispatcher.BeginInvoke` in Windows Phone.

Il codice di xamarin. Forms è:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Si noti che tramite i metodi `async/await` non è necessario utilizzare `BeginInvokeOnMainThread` se vengono eseguiti dal thread principale dell'interfaccia utente.

## <a name="summary"></a>Riepilogo

Di xamarin. Forms `Device` classe consente a un controllo accurato sul layout e funzionalità di base per ogni piattaforma, anche in comune di codice (progetti di libreria di classi Portabile o condiviso).


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di dispositivo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Esempio di stili](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dispositivo](https://developer.xamarin.com/api/type/Xamarin.Forms.Device/)
