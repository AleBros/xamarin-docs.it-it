---
title: Classe del dispositivo Novell. Forms
description: Questo articolo illustra come usare la classe di dispositivi Novell. Forms, per un controllo con granularità fine sulle funzionalità e i layout in base alla piattaforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 25ddbea75d0fd6858f848499281da5d5f0b68171
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697193"
---
# <a name="xamarinforms-device-class"></a>Classe del dispositivo Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

La classe [`Device`](xref:Xamarin.Forms.Device) contiene una serie di proprietà e metodi che consentono agli sviluppatori di personalizzare il layout e le funzionalità in base alle singole piattaforme.

Oltre ai metodi e alle proprietà per il codice di destinazione con tipi e dimensioni hardware specifici, la classe `Device` include metodi che possono essere usati per interagire con i controlli dell'interfaccia utente dai thread in background. Per altre informazioni, vedere [interagire con l'interfaccia utente dai thread in background](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Fornire valori specifici della piattaforma

Prima di Novell. Forms 2.3.4, è possibile ottenere la piattaforma in cui è in esecuzione l'applicazione esaminando la proprietà [`Device.OS`](xref:Xamarin.Forms.Device.OS) e confrontandolo con l'enumerazione [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS), [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android), [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)e [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) valori. Analogamente, è possibile usare uno degli overload [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) per fornire valori specifici della piattaforma a un controllo.

Tuttavia, poiché Novell. Forms 2.3.4 queste API sono state deprecate e sostituite. La classe [`Device`](xref:Xamarin.Forms.Device) ora contiene costanti di stringa pubbliche che identificano le piattaforme: [`Device.iOS`](xref:Xamarin.Forms.Device.iOS), [`Device.Android`](xref:Xamarin.Forms.Device.Android), `Device.WinPhone` (deprecato), `Device.WinRT` (deprecato), [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)e [1](xref:Xamarin.Forms.Device.macOS). Analogamente, gli overload [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) sono stati sostituiti con le API [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) .

In C#i valori specifici della piattaforma possono essere forniti creando un'istruzione `switch` sulla proprietà [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) e quindi specificando `case` istruzioni per le piattaforme richieste:

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

Le classi [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) forniscono la stessa funzionalità in XAML:

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

La classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) è una classe generica di cui è necessario creare un'istanza con un attributo `x:TypeArguments` corrispondente al tipo di destinazione. Nella classe [`On`](xref:Xamarin.Forms.On) l'attributo [`Platform`](xref:Xamarin.Forms.On.Platform) può accettare un singolo valore di `string` o più valori `string` delimitati da virgole.

> [!IMPORTANT]
> Se si specifica un valore di attributo `Platform` errato nella classe `On`, non verrà generato alcun errore. Al contrario, il codice verrà eseguito senza che venga applicato il valore specifico della piattaforma.

In alternativa, è possibile usare l'estensione di markup `OnPlatform` in XAML per personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma. Per altre informazioni, vedere [estensione di markup onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Device. Idiom

La proprietà `Device.Idiom` può essere usata per modificare layout o funzionalità in base al dispositivo in cui è in esecuzione l'applicazione. L'enumerazione [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) contiene i valori seguenti:

- **Telefono** : dispositivi iPhone, iPod touch e Android più ristretti di 600 DIP ^
- **Tablet** : iPad, dispositivi Windows e dispositivi Android più ampi di 600 DIP ^
- **Desktop** : viene restituito solo nelle [app UWP](~/xamarin-forms/platform/windows/installation/index.md) nei computer desktop windows 10 (restituisce `Phone` nei dispositivi Windows mobili, incluso negli scenari continuum)
- **TV** -dispositivi Tizen TV
- **Guardare** : Tizen Watch Devices
- Non **supportato** : non usato

*^ DIP non è necessariamente il numero di pixel fisici*

La proprietà `Idiom` è particolarmente utile per la creazione di layout che sfruttano le schermate più grandi, come indicato di seguito:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

La classe [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) fornisce la stessa funzionalità in XAML:

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

La classe [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) è una classe generica di cui è necessario creare un'istanza con un attributo `x:TypeArguments` corrispondente al tipo di destinazione.

In alternativa, è possibile usare l'estensione di markup `OnIdiom` in XAML per personalizzare l'aspetto dell'interfaccia utente in base all'idioma del dispositivo in cui è in esecuzione l'applicazione. Per altre informazioni, vedere [estensione di markup onidiom](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device. FlowDirection

Il valore [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) recupera un valore di enumerazione [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) che rappresenta la direzione del flusso corrente utilizzata dal dispositivo. La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. I valori dell'enumerazione sono:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

In XAML è possibile recuperare il valore [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) usando l'estensione di markup `x:Static`:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Il codice equivalente in C# è:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. Styles

La [proprietà `Styles`](~/xamarin-forms/user-interface/styles/index.md) contiene le definizioni di stile predefinite che è possibile applicare ad alcuni controlli, ad esempio `Label` `Style` proprietà. Gli stili disponibili sono:

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device. GetNamedSize

`GetNamedSize` può essere utilizzato quando si imposta [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) C# nel codice:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicestarttimer"></a>Device. StartTimer

La classe `Device` dispone anche di un metodo `StartTimer` che fornisce un modo semplice per attivare attività dipendenti dal tempo che funzionano nel codice comune di Novell. Forms, inclusa una libreria di .NET Standard. Passare un `TimeSpan` per impostare l'intervallo e restituire `true` per impedire l'esecuzione del timer oppure `false` arrestarlo dopo la chiamata corrente.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se il codice all'interno del timer interagisce con l'interfaccia utente, ad esempio impostando il testo di un `Label` o visualizzando un avviso, è necessario eseguire l'operazione all'interno di un'espressione `BeginInvokeOnMainThread` (vedere di seguito).

> [!NOTE]
> Le classi `System.Timers.Timer` e `System.Threading.Timer` sono .NET Standard alternative all'utilizzo del metodo `Device.StartTimer`.

## <a name="interact-with-the-ui-from-background-threads"></a>Interagire con l'interfaccia utente dai thread in background

La maggior parte dei sistemi operativi, tra cui iOS, Android e la piattaforma UWP (Universal Windows Platform), usa un modello a thread singolo per il codice che interessa l'interfaccia utente. Questo thread viene spesso definito *thread principale* o thread dell' *interfaccia utente*. Una conseguenza di questo modello è che tutto il codice che accede agli elementi dell'interfaccia utente deve essere eseguito sul thread principale dell'applicazione.

Le applicazioni a volte usano i thread in background per eseguire operazioni potenzialmente a esecuzione prolungata, ad esempio il recupero di dati da un servizio Web. Se il codice in esecuzione in un thread in background deve accedere agli elementi dell'interfaccia utente, deve eseguire tale codice sul thread principale.

La classe `Device` include i seguenti metodi di `static` che possono essere usati per interagire con gli elementi dell'interfaccia utente dai thread di background:

| Metodo | Argomenti | Valori di codice restituiti | Scopo |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Richiama un `Action` sul thread principale e non ne attende il completamento. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Richiama un oggetto `Func<T>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Richiama un oggetto `Action` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Richiama un oggetto `Func<Task<T>>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Richiama un oggetto `Func<Task>` sul thread principale e ne attende il completamento. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Restituisce l'oggetto `SynchronizationContext` per il thread principale. |

Il codice seguente illustra un esempio di utilizzo del metodo `BeginInvokeOnMainThread`:

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
