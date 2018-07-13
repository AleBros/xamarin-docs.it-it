---
title: Implementazione della sintesi vocale
description: Questo articolo illustra come usare la classe di xamarin. Forms DependencyService chiamare API sintesi vocale di entrambe le piattaforme native.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: d39902f2269d3eb241b48831b8eb1b181ff11e7a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997543"
---
# <a name="implementing-text-to-speech"></a>Implementazione della sintesi vocale

Questo articolo illustrerà come durante la creazione di un'app multipiattaforma che usa [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) per accedere alle API sintesi vocale native:

- **[Creazione dell'interfaccia](#Creating_the_Interface)**  &ndash; comprendere come viene creata l'interfaccia nel codice condiviso.
- **[Implementazione di iOS](#iOS_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione di Android](#Android_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione di UWP](#WindowsImplementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per la Universal Windows Platform (UWP).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)**  &ndash; informazioni su come usare `DependencyService` chiamino l'implementazione native da codice condiviso.

L'applicazione usando `DependencyService` avrà la struttura seguente:

![](text-to-speech-images/tts-diagram.png "Struttura dell'applicazione DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Innanzitutto, creare un'interfaccia nel codice condiviso che esprime la funzionalità che si prevede di implementare. Per questo esempio, l'interfaccia contiene un solo metodo, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Scrivere il codice per questa interfaccia nel codice condiviso consentirà all'app xamarin. Forms di accedere l'API di riconoscimento vocale in ogni piattaforma.

> [!NOTE]
> Classi che implementano l'interfaccia devono avere un costruttore senza parametri per lavorare con i `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione di iOS

L'interfaccia deve essere implementata in ogni progetto specifico della piattaforma dell'applicazione. Si noti che la classe ha un costruttore senza parametri in modo che il `DependencyService` può creare nuove istanze.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.iOS
{

    public class TextToSpeechImplementation : ITextToSpeech
    {
        public TextToSpeechImplementation() { }

        public void Speak(string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer();
            var speechUtterance = new AVSpeechUtterance(text)
            {
                Rate = AVSpeechUtterance.MaximumSpeechRate / 4,
                Voice = AVSpeechSynthesisVoice.FromLanguage("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance(speechUtterance);
        }
    }
}
```

Il `[assembly]` attributo registra la classe come implementazione del `ITextToSpeech` interfaccia, il che significa che `DependencyService.Get<ITextToSpeech>()` utilizzabile nel codice condiviso per creare un'istanza.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione di Android

Il codice Android è più complesso rispetto alla versione di iOS: richiede la classe di implementazione in modo che erediti da Android specifiche `Java.Lang.Object` e implementare il `IOnInitListener` anche l'interfaccia. Richiede anche l'accesso al contesto di Android corrente, che viene esposto dal `MainActivity.Instance` proprietà.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.Droid
{
    public class TextToSpeechImplementation : Java.Lang.Object, ITextToSpeech, TextToSpeech.IOnInitListener
    {
        TextToSpeech speaker;
        string toSpeak;

        public void Speak(string text)
        {
            toSpeak = text;
            if (speaker == null)
            {
                speaker = new TextToSpeech(MainActivity.Instance, this);
            }
            else
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }

        public void OnInit(OperationResult status)
        {
            if (status.Equals(OperationResult.Success))
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }
    }
}
```

Il `[assembly]` attributo registra la classe come implementazione del `ITextToSpeech` interfaccia, il che significa che `DependencyService.Get<ITextToSpeech>()` utilizzabile nel codice condiviso per creare un'istanza.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione della piattaforma Windows universale

La piattaforma Windows universale è un'API traduzione vocale nel `Windows.Media.SpeechSynthesis` dello spazio dei nomi. L'unico problema è ricordarsi di selezionare il **microfono** funzionalità nel manifesto, altrimenti accedere per il riconoscimento vocale vengono bloccate le API.

```csharp
[assembly:Dependency(typeof(TextToSpeechImplementation))]
public class TextToSpeechImplementation : ITextToSpeech
{
    public async void Speak(string text)
    {
        var mediaElement = new MediaElement();
        var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
        var stream = await synth.SynthesizeTextToStreamAsync(text);

        mediaElement.SetSource(stream, stream.ContentType);
        mediaElement.Play();
    }
}
```

Il `[assembly]` attributo registra la classe come implementazione del `ITextToSpeech` interfaccia, il che significa che `DependencyService.Get<ITextToSpeech>()` utilizzabile nel codice condiviso per creare un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

A questo punto è possibile scrivere e testare il codice condiviso che accede all'interfaccia di sintesi vocale. Questa pagina semplice include un pulsante che attiva la funzionalità di riconoscimento vocale. Usa il `DependencyService` per ottenere un'istanza del `ITextToSpeech` interfaccia &ndash; in fase di esecuzione questa istanza sarà l'implementazione specifica della piattaforma che dispone dell'accesso completo a SDK nativi.

```csharp
public MainPage ()
{
    var speak = new Button {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    speak.Clicked += (sender, e) => {
        DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
    };
    Content = speak;
}
```

Esecuzione di questa applicazione su iOS, Android o la piattaforma UWP e premendo il pulsante comporterà l'applicazione a proposito all'utente, utilizzando il parlato SDK nativo in ciascuna piattaforma.

 ![iOS e Android pulsante sintesi vocale](text-to-speech-images/running.png "esempio sintesi vocale")


## <a name="related-links"></a>Collegamenti correlati

- [Con DependencyService (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
- [Cartella di lavoro di sintesi vocale](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/text-to-speech/text-to-speech.workbook)
