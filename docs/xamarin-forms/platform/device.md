---
title: Classe del dispositivo Novell. Forms
description: Questo articolo illustra come usare la classe di dispositivi Novell. Forms, per un controllo con granularità fine sulle funzionalità e i layout in base alla piattaforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2020
ms.openlocfilehash: d0f0fa7dd68e8852dd7a72486c155ec064540644
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517062"
---
# <a name="xamarinforms-device-class"></a>Classe del dispositivo Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

La [`Device`](xref:Xamarin.Forms.Device) classe contiene una serie di proprietà e metodi che consentono agli sviluppatori di personalizzare il layout e le funzionalità per ogni singola piattaforma.

Oltre ai metodi e alle proprietà per il codice di destinazione con tipi e dimensioni hardware specifici `Device` , la classe include metodi che possono essere usati per interagire con i controlli dell'interfaccia utente dai thread in background. Per altre informazioni, vedere [interagire con l'interfaccia utente dai thread in background](#interact-with-the-ui-from-background-threads).

## <a name="provide-platform-specific-values"></a>Fornire valori specifici della piattaforma

Prima di Novell. Forms 2.3.4, è possibile ottenere la piattaforma in cui è in esecuzione l'applicazione [`Device.OS`](xref:Xamarin.Forms.Device.OS) esaminando la proprietà e confrontandolo con [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS)i [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android)valori [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)di enumerazione [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) ,, e. Analogamente, è possibile [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) usare uno degli overload per fornire valori specifici della piattaforma a un controllo.

Tuttavia, poiché Novell. Forms 2.3.4 queste API sono state deprecate e sostituite. La [`Device`](xref:Xamarin.Forms.Device) classe contiene ora costanti di stringa pubbliche che identificano le [`Device.iOS`](xref:Xamarin.Forms.Device.iOS)piattaforme [`Device.Android`](xref:Xamarin.Forms.Device.Android): `Device.WinPhone`,, (deprecato `Device.WinRT` ), (deprecato [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)), [`Device.macOS`](xref:Xamarin.Forms.Device.macOS)e. Analogamente, [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) gli overload sono stati sostituiti con le [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) API [`On`](xref:Xamarin.Forms.On) e.

In C# è possibile fornire valori specifici della `switch` [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) piattaforma creando un'istruzione sulla proprietà e quindi fornendo `case` istruzioni per le piattaforme richieste:

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

Le [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classi [`On`](xref:Xamarin.Forms.On) e forniscono la stessa funzionalità in XAML:

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

La [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe è una classe generica di cui è necessario creare un' `x:TypeArguments` istanza con un attributo che corrisponda al tipo di destinazione. Nella [`On`](xref:Xamarin.Forms.On) classe l' [`Platform`](xref:Xamarin.Forms.On.Platform) attributo può accettare un singolo `string` valore o più valori delimitati `string` da virgole.

> [!IMPORTANT]
> Se non si `Platform` specifica un valore di `On` attributo errato nella classe, non verrà generato alcun errore. Al contrario, il codice verrà eseguito senza che venga applicato il valore specifico della piattaforma.

In alternativa, è `OnPlatform` possibile usare l'estensione di markup in XAML per personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma. Per altre informazioni, vedere [estensione di markup onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="deviceidiom"></a>Device. Idiom

La `Device.Idiom` proprietà può essere usata per modificare layout o funzionalità in base al dispositivo in cui è in esecuzione l'applicazione. L' [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) enumerazione contiene i valori seguenti:

- **Telefono** : dispositivi iPhone, iPod touch e Android più ristretti di 600 DIP ^
- **Tablet** : iPad, dispositivi Windows e dispositivi Android più ampi di 600 DIP ^
- **Desktop** : viene restituito solo nelle [app UWP](~/xamarin-forms/platform/windows/installation/index.md) nei computer desktop Windows 10 ( `Phone` viene restituito nei dispositivi Windows mobili, incluso negli scenari di continuità)
- **TV** -dispositivi Tizen TV
- **Guardare** : Tizen Watch Devices
- Non **supportato** : non usato

*^ DIP non è necessariamente il numero di pixel fisici*

La `Idiom` proprietà è particolarmente utile per la creazione di layout che sfruttano le schermate più grandi, come indicato di seguito:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) classe fornisce la stessa funzionalità in XAML:

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

La [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) classe è una classe generica di cui è necessario creare un' `x:TypeArguments` istanza con un attributo che corrisponda al tipo di destinazione.

In alternativa, è `OnIdiom` possibile usare l'estensione di markup in XAML per personalizzare l'aspetto dell'interfaccia utente in base all'idioma del dispositivo in cui è in esecuzione l'applicazione. Per altre informazioni, vedere [estensione di markup onidiom](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device. FlowDirection

Il [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valore recupera un [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) valore di enumerazione che rappresenta la direzione del flusso corrente utilizzata dal dispositivo. La direzione del flusso è la direzione in cui gli elementi dell'interfaccia utente della pagina vengono analizzati dall'occhio del lettore. I valori dell'enumerazione sono:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

In XAML è possibile [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) recuperare il valore usando l' `x:Static` estensione di markup:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Il codice equivalente in C# è:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. Styles

La [ `Styles` proprietà](~/xamarin-forms/user-interface/styles/index.md) contiene le definizioni di stile predefinite che è possibile applicare alla `Style` proprietà di alcuni controlli, ad `Label`esempio. Gli stili disponibili sono:

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device. GetNamedSize

`GetNamedSize`può essere usato quando si [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) imposta nel codice C#:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>Device. GetNamedColor

Novell. Forms 4,6 introduce il supporto per i colori denominati. Un colore denominato è un colore con un valore diverso a seconda della modalità di sistema (ad esempio, chiaro o scuro) attiva sul dispositivo. In Android è possibile accedere ai colori denominati tramite la classe [R. Color](https://developer.android.com/reference/android/R.color#constants_2) . In iOS i colori denominati sono detti [colori di sistema](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors). Nella piattaforma UWP (Universal Windows Platform) i colori denominati sono detti [risorse del tema XAML](/windows/uwp/design/controls-and-patterns/xaml-theme-resources).

Il `GetNamedColor` metodo può essere usato per recuperare i colori denominati in Android, iOS e UWP. Il metodo accetta un `string` argomento e restituisce un [`Color`](xref:Xamarin.Forms.Color)oggetto:

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default`viene restituito quando non è possibile trovare un nome di colore o quando `GetNamedColor` viene richiamato in una piattaforma non supportata.

> [!NOTE]
> Poiché il `GetNamedColor` metodo restituisce un `Color` oggetto specifico di una piattaforma, in genere deve essere usato in combinazione con la [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) proprietà.

La `NamedPlatformColor` classe contiene le costanti che definiscono i colori denominati per Android, iOS e UWP:

| Android | iOS | UWP |
| --- | --- | --- |
| `BackgroundDark` | `Label` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `SystemChromeDisabledHighColor` |
| `White` | `SystemListLowColor` | `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemListMediumColor` | `SystemChromeHighColor` |
| | `SystemPink` | `SystemChromeLowColor` |
| | `SystemPurple` | `SystemChromeMediumColor` |
| | `SystemRed` | `SystemChromeMediumLowColor` |
| | `SystemTeal` | `SystemChromeWhiteColor` |
| | `SystemYellow` |
| | `TertiaryLabel` |

## <a name="devicestarttimer"></a>Device. StartTimer

La `Device` classe dispone anche di `StartTimer` un metodo che fornisce un modo semplice per attivare attività dipendenti dal tempo che funzionano nel codice comune di Novell. Forms, inclusa una libreria di .NET standard. Passare un `TimeSpan` oggetto per impostare l'intervallo e `true` restituire per lasciare l'esecuzione del `false` timer o per arrestarla dopo la chiamata corrente.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Se il codice all'interno del timer interagisce con l'interfaccia utente, ad esempio impostando il testo di `Label` un oggetto o visualizzando un avviso, è necessario eseguire l' `BeginInvokeOnMainThread` operazione all'interno di un'espressione (vedere di seguito).

> [!NOTE]
> Le `System.Timers.Timer` classi `System.Threading.Timer` e sono .NET standard alternative all'utilizzo del `Device.StartTimer` metodo.

## <a name="interact-with-the-ui-from-background-threads"></a>Interagire con l'interfaccia utente dai thread in background

La maggior parte dei sistemi operativi, tra cui iOS, Android e la piattaforma UWP (Universal Windows Platform), usa un modello a thread singolo per il codice che interessa l'interfaccia utente. Questo thread viene spesso definito *thread principale* o thread dell' *interfaccia utente*. Una conseguenza di questo modello è che tutto il codice che accede agli elementi dell'interfaccia utente deve essere eseguito sul thread principale dell'applicazione.

Le applicazioni a volte usano i thread in background per eseguire operazioni potenzialmente a esecuzione prolungata, ad esempio il recupero di dati da un servizio Web. Se il codice in esecuzione in un thread in background deve accedere agli elementi dell'interfaccia utente, deve eseguire tale codice sul thread principale.

La `Device` classe include i metodi `static` seguenti che possono essere usati per interagire con gli elementi dell'interfaccia utente dai thread di background:

| Metodo | Argomenti | Valori di codice restituiti | Scopo |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Richiama un oggetto `Action` sul thread principale e non ne attende il completamento. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Richiama un oggetto `Func<T>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Richiama un oggetto `Action` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Richiama un oggetto `Func<Task<T>>` sul thread principale e ne attende il completamento. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Richiama un oggetto `Func<Task>` sul thread principale e ne attende il completamento. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Restituisce l'oggetto `SynchronizationContext` per il thread principale. |

Il codice seguente illustra un esempio di utilizzo del `BeginInvokeOnMainThread` metodo:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Link correlati

- [Esempio di dispositivo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Esempio di stili](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [API del dispositivo](xref:Xamarin.Forms.Device)
