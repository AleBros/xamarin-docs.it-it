---
title: Classe di dispositivi di xamarin. Forms
description: Questo articolo illustra come usare la classe di dispositivi di xamarin. Forms, per un controllo accurato sul layout e funzionalità in base a ogni piattaforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 1aacd9a29ca13335d14f66175b2d2a4ccb19c9dc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655961"
---
# <a name="xamarinforms-device-class"></a>Classe di dispositivi di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

Il [ `Device` ](xref:Xamarin.Forms.Device) classe contiene un numero di proprietà e metodi che consentono agli sviluppatori di personalizzare i layout e le funzionalità in base a ogni piattaforma.

Oltre ai metodi e alle proprietà per il codice di destinazione con tipi e dimensioni hardware specifici `Device` , la classe include metodi che possono essere usati per interagire con i controlli dell'interfaccia utente dai thread in background. Per altre informazioni, vedere [interagire con l'interfaccia utente dai thread in background](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Fornire valori specifici della piattaforma

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

## <a name="devicestyles"></a>Device.Styles

Il [ `Styles` proprietà](~/xamarin-forms/user-interface/styles/index.md) contiene le definizioni di stile incorporato che possono essere applicate a alcuni controlli (ad esempio `Label`) `Style` proprietà. Gli stili disponibili sono:

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

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

## <a name="deviceopenuri"></a>Device.OpenUri

Il `OpenUri` metodo può essere utilizzato per attivare operazioni nella piattaforma sottostante, ad esempio aprire un URL nel browser web nativi (**Safari** in iOS o **Internet** in Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

Il [esempio di WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) include un esempio di utilizzo `OpenUri` per aprire gli URL e anche attivare le chiamate telefoniche.

Il [esempio di mappe](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) Usa anche `Device.OpenUri` per visualizzare le mappe e le direzioni utilizzando l'oggetto nativo **esegue il mapping** App in iOS e Android.

## <a name="devicestarttimer"></a>Device.StartTimer

Il `Device` classe include anche un `StartTimer` metodo che fornisce un modo semplice per attivare le attività dipendenti dal tempo utilizzabile nel codice comune con xamarin. Forms, inclusa una libreria .NET Standard. Passare un `TimeSpan` per impostare l'intervallo e restituire `true` mantenere il timer in esecuzione o `false` interromperlo dopo la chiamata corrente.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se il codice all'interno del timer interagisce con l'interfaccia utente (ad esempio l'impostazione del testo di un `Label` oppure che venga visualizzato un avviso) deve essere eseguita all'interno di un `BeginInvokeOnMainThread` espressione (vedere sotto).

## <a name="interact-with-the-ui-from-background-threads"></a>Interagire con l'interfaccia utente dai thread in background

La maggior parte dei sistemi operativi, tra cui iOS, Android e la piattaforma UWP (Universal Windows Platform), usa un modello a thread singolo per il codice che interessa l'interfaccia utente. Questo thread viene spesso definito *thread principale* o thread dell' *interfaccia utente*. Una conseguenza di questo modello è che tutto il codice che accede agli elementi dell'interfaccia utente deve essere eseguito sul thread principale dell'applicazione.

Le applicazioni a volte usano i thread in background per eseguire operazioni potenzialmente a esecuzione prolungata, ad esempio il recupero di dati da un servizio Web. Se il codice in esecuzione in un thread in background deve accedere agli elementi dell'interfaccia utente, deve eseguire tale codice sul thread principale.

La `Device` classe include i metodi `static` seguenti che possono essere usati per interagire con gli elementi dell'interfaccia utente dai thread di background:

| Metodo | Argomenti | Valore restituito | Scopo |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Richiama un oggetto `Action` sul thread principale e non ne attende il completamento. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Richiama un oggetto `Func<T>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Richiama un oggetto `Action` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Richiama un oggetto `Func<Task<T>>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Richiama un oggetto `Func<Task>` sul thread principale e ne attende il completamento. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Restituisce l' `SynchronizationContext` oggetto per il thread principale. |

Il codice seguente illustra un esempio di utilizzo del `BeginInvokeOnMainThread` metodo:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di dispositivo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Esempio di stili](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Dispositivo](xref:Xamarin.Forms.Device)
