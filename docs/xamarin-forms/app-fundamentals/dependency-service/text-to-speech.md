---
title: Implementazione di sintesi vocale
description: Utilizzare DependencyService eseguire chiamate nell'API di sintesi vocale native di ciascuna piattaforma
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: c9cf700ea798ac316e806c40cb90eedc7ded9fa5
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="implementing-text-to-speech"></a>Implementazione di sintesi vocale

In questo articolo guiderà l'utente durante la creazione di un'app multipiattaforma che usa [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) per accedere ad API sintesi vocale native:

- **[La creazione dell'interfaccia](#Creating_the_Interface)**  &ndash; comprendere la modalità di creazione dell'interfaccia nel codice condiviso.
- **[iOS implementazione](#iOS_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione Android](#Android_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione UWP](#WindowsImplementation)**  &ndash; imparare a implementare l'interfaccia nel codice nativo per la piattaforma UWP (Universal Windows).
- **[Implementazione di codice condiviso](#Implementing_in_Shared_Code)**  &ndash; imparare a usare `DependencyService` chiamino l'implementazione native da codice condiviso.

L'applicazione utilizzando `DependencyService` avrà la struttura seguente:

![](text-to-speech-images/tts-diagram.png "Struttura dell'applicazione DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Innanzitutto, creare un'interfaccia nel codice condiviso che esprime la funzionalità che si intende implementare. Per questo esempio, l'interfaccia contiene un singolo metodo, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Scrivere codice per questa interfaccia nel codice condiviso consentirà l'app xamarin. Forms accedere il riconoscimento vocale API in ciascuna piattaforma.

> [!NOTE]
> Le classi che implementano l'interfaccia devono avere un costruttore senza parametri per funzionare con il `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione di iOS

L'interfaccia deve essere implementata in ogni progetto di applicazione specifico della piattaforma. Si noti che la classe ha un costruttore senza parametri in modo che il `DependencyService` possibile creare nuove istanze.

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

Il `[assembly]` attributo consente di registrare la classe come un'implementazione del `ITextToSpeech` interfaccia, il che significa che `DependencyService.Get<ITextToSpeech>()` può essere utilizzato nel codice condiviso per creare un'istanza.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione di Android

Il codice Android è più complesso rispetto alla versione di iOS: richiede la classe di implementazione per ereditare da Android specifico `Java.Lang.Object` e implementare il `IOnInitListener` anche l'interfaccia. È inoltre necessario accedere al contesto di Android corrente, che viene esposto dal `MainActivity.Instance` proprietà.

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

Il `[assembly]` attributo consente di registrare la classe come un'implementazione del `ITextToSpeech` interfaccia, il che significa che `DependencyService.Get<ITextToSpeech>()` può essere utilizzato nel codice condiviso per creare un'istanza.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione della piattaforma Windows universale

La piattaforma Windows universale è un'API di sintesi vocale nel `Windows.Media.SpeechSynthesis` dello spazio dei nomi. L'unico problema è da ricordare tick il **microfono** funzionalità nel manifesto, in caso contrario l'accesso a del contenuto vocale API sono bloccate.

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

Il `[assembly]` attributo consente di registrare la classe come un'implementazione del `ITextToSpeech` interfaccia, il che significa che `DependencyService.Get<ITextToSpeech>()` può essere utilizzato nel codice condiviso per creare un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione di codice condiviso

Ora è possibile scrivere e testare il codice condiviso che accede all'interfaccia di sintesi vocale. Questa pagina semplice include un pulsante che attiva la funzionalità di riconoscimento vocale. Usa il `DependencyService` per ottenere un'istanza del `ITextToSpeech` interfaccia &ndash; in fase di esecuzione questa istanza è l'implementazione specifica della piattaforma che dispone dell'accesso completo a SDK nativo.

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

Questa applicazione in esecuzione iOS, Android o alla piattaforma UWP e premendo il pulsante comporterà l'applicazione di pronuncia all'utente, tramite il riconoscimento vocale native SDK in ciascuna piattaforma.

 ![iOS e Android pulsante sintesi vocale](text-to-speech-images/running.png "esempio sintesi vocale")


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzando DependencyService (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
- [Cartella di lavoro di sintesi vocale](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/text-to-speech/text-to-speech.workbook)
