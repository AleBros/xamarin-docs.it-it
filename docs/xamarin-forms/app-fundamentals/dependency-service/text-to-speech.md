---
title: Implementazione della sintesi vocale
description: Questo articolo illustra come usare la classe Xamarin.Forms DependencyService per eseguire chiamate nelle API native di sintesi vocale in ogni piattaforma.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 6d1948214b97a1b536b07b6420c32e4d27124518
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "38997543"
---
# <a name="implementing-text-to-speech"></a>Implementazione della sintesi vocale

Questo articolo illustrerà la procedura per creare un'app multipiattaforma che usa [`DependencyService`](xref:Xamarin.Forms.DependencyService) per accedere alle API native della sintesi vocale:

- **[Creazione dell'interfaccia](#Creating_the_Interface)**: informazioni sulla creazione dell'interfaccia nel codice condiviso.
- **[Implementazione in iOS](#iOS_Implementation)**: informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione in Android](#Android_Implementation)**: informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione nella piattaforma UWP](#WindowsImplementation)**: informazioni su come implementare l'interfaccia nel codice nativo per la piattaforma UWP (Universal Windows Platform).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)**: informazioni su come usare `DependencyService` per eseguire chiamate nell'implementazione nativa dal codice condiviso.

L'applicazione che usa `DependencyService` avrà la struttura seguente:

![](text-to-speech-images/tts-diagram.png "Struttura dell'applicazione DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Creare prima di tutto un'interfaccia nel codice condiviso che esprime la funzionalità che si prevede di implementare. Per questo esempio, l'interfaccia contiene un solo metodo, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

La scrittura del codice per questa interfaccia nel codice condiviso consentirà all'app Xamarin.Forms di accedere alle API della sintesi vocale in ogni piattaforma.

> [!NOTE]
> Le classi che implementano l'interfaccia devono avere un costruttore senza parametri per usare `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione in iOS

L'interfaccia deve essere implementata in ogni progetto di applicazione specifico della piattaforma. Si noti che la classe ha un costruttore senza parametri in modo che `DependencyService` possa creare nuove istanze.

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

L'attributo `[assembly]` registra la classe come implementazione dell'interfaccia `ITextToSpeech` e ciò significa che è possibile usare `DependencyService.Get<ITextToSpeech>()` nel codice condiviso per creare un'istanza.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione in Android

Il codice Android è più complesso rispetto alla versione di iOS: richiede che la classe di implementazione erediti dall'elemento `Java.Lang.Object` specifico di Android e che venga implementata anche l'interfaccia `IOnInitListener`. Richiede anche l'accesso al contesto di Android corrente, che viene esposto dalla proprietà `MainActivity.Instance`.

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

L'attributo `[assembly]` registra la classe come implementazione dell'interfaccia `ITextToSpeech` e ciò significa che è possibile usare `DependencyService.Get<ITextToSpeech>()` nel codice condiviso per creare un'istanza.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione nella piattaforma UWP

La piattaforma UWP ha un'API di sintesi vocale nello spazio dei nomi `Windows.Media.SpeechSynthesis`. L'unico problema è ricordarsi di selezionare la funzionalità **Microfono** nel manifesto. In caso contrario, l'accesso all'API della sintesi vocale risulterà bloccato.

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

L'attributo `[assembly]` registra la classe come implementazione dell'interfaccia `ITextToSpeech` e ciò significa che è possibile usare `DependencyService.Get<ITextToSpeech>()` nel codice condiviso per creare un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

A questo punto è possibile scrivere e testare il codice condiviso che consente di accedere all'interfaccia della sintesi vocale. Questa semplice pagina include un pulsante che attiva la funzionalità di sintesi vocale. Usa `DependencyService` per ottenere un'istanza dell'interfaccia `ITextToSpeech`. In fase di esecuzione questa istanza sarà l'implementazione specifica della piattaforma con accesso completo all'SDK nativo.

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

Quando si esegue l'applicazione in iOS, Android o UWP e si seleziona il pulsante, l'applicazione inizia a parlare, usando l'SDK nativo della sintesi vocale per ogni piattaforma.

 ![Pulsante della sintesi vocale per iOS e Android](text-to-speech-images/running.png "Esempio della sintesi vocale")


## <a name="related-links"></a>Collegamenti correlati

- [Using DependencyService (sample)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/) (Uso di DependencyService (esempio))
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)

