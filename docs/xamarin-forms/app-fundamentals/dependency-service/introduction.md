---
title: Introduzione a DependencyService
description: Questo articolo illustra il funzionamento della classe Xamarin.Forms DependencyService per accedere alle funzionalità native della piattaforma.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675016"
---
# <a name="introduction-to-dependencyservice"></a>Introduzione a DependencyService

## <a name="overview"></a>Panoramica

[`DependencyService`](xref:Xamarin.Forms.DependencyService) consente alle app di chiamare funzionalità specifiche della piattaforma dal codice condiviso. Questa funzionalità consente alle app Xamarin.Forms di eseguire qualsiasi operazione che può essere eseguita da un'app nativa.

`DependencyService` è un localizzatore di servizi. In pratica, viene definita un'interfaccia e `DependencyService` trova la corretta implementazione di tale interfaccia dai vari progetti della piattaforma.

> [!NOTE]
> Per impostazione predefinita, la classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) risolverà solo le implementazioni della piattaforma che hanno costruttori senza parametri. È tuttavia possibile inserire in Xamarin.Forms un metodo di risoluzione delle dipendenze che usa un contenitore di inserimento delle dipendenze o metodi factory per risolvere le implementazioni della piattaforma. Questo approccio può essere usato per risolvere le implementazioni della piattaforma che hanno costruttori con parametri. Per altre informazioni, vedere [Risoluzione delle dipendenze in Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Come funziona DependencyService

Le app Xamarin.Forms necessitano di quattro componenti per usare `DependencyService`:

- **Interfaccia**: la funzionalità necessaria è definita da un'interfaccia nel codice condiviso.
- **Implementazione per ogni piattaforma**: le classi che implementano l'interfaccia devono essere aggiunte a ogni progetto della piattaforma.
- **Registrazione**: ogni classe di implementazione deve essere registrata con `DependencyService` tramite un attributo di metadati. La registrazione consente a `DependencyService` di trovare la classe di implementazione e specificarla al posto dell'interfaccia in fase di esecuzione.
- **Chiamata a DependencyService**: il codice condiviso deve chiamare in modo esplicito `DependencyService` per richiedere le implementazioni dell'interfaccia.

Si noti che è necessario specificare le implementazioni per ogni progetto della piattaforma nella soluzione. I progetti della piattaforma senza alcuna implementazione avranno esito negativo in fase di esecuzione.

La struttura dell'applicazione è illustrata nel diagramma seguente:

![](introduction-images/overview-diagram.png "Struttura dell'applicazione DependencyService")

### <a name="interface"></a>Interfaccia

L'interfaccia progettata definirà il modo di interazione dell'utente con le funzionalità specifiche della piattaforma. Prestare attenzione se si sta sviluppando un componente da condividere come componente o pacchetto NuGet. La progettazione di API può stabilire o interrompere un pacchetto. L'esempio seguente specifica un'interfaccia semplice per la pronuncia di testo che consente una flessibilità nello specificare le parole da pronunciare ma lascia l'implementazione aperta alla personalizzazione per ogni piattaforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementazione per ogni piattaforma

Dopo aver sviluppato un'interfaccia appropriata, essa deve essere implementata nel progetto per ogni piattaforma di destinazione. Ad esempio, la classe seguente implementa l'interfaccia `ITextToSpeech` in iOS:

```csharp
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

### <a name="registration"></a>Registrazione

Ogni implementazione dell'interfaccia deve essere registrata con `DependencyService` con un attributo di metadati. Il codice seguente registra l'implementazione per iOS:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

Riassumendo, l'implementazione specifica della piattaforma ha un aspetto simile al seguente:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

Nota: la registrazione viene eseguita a livello di spazio dei nomi, non a livello di classe.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilazione .NET Native per la piattaforma UWP (Universal Windows Platform)

I progetti UWP che usano l'opzione di compilazione .NET Native devono seguire una [configurazione leggermente diversa](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) durante l'inizializzazione di Xamarin.Forms. La compilazione .NET Native richiede anche una registrazione leggermente diversa per i servizi di dipendenza.

Nel file **App.xaml.cs** registrare manualmente ogni servizio di dipendenza definito nel progetto UWP usando il metodo `Register<T>`, come illustrato di seguito:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Nota: la registrazione manuale tramite `Register<T>` è efficace solo nelle build di rilascio con compilazione .NET Native. Se si omette questa riga, le build di debug continueranno a funzionare, ma le build di rilascio non riusciranno a caricare il servizio di dipendenza.

### <a name="call-to-dependencyservice"></a>Chiamata a DependencyService

Dopo che il progetto è stato impostato con un'interfaccia comune e le implementazioni per ogni piattaforma, usare `DependencyService` per ottenere l'implementazione corretta in fase di esecuzione:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` troverà la corretta implementazione dell'interfaccia `T`.

### <a name="solution-structure"></a>Struttura della soluzione

Di seguito è illustrata la [soluzione UsingDependencyService di esempio](https://developer.xamarin.com/samples/UsingDependencyService/) per iOS e Android, con le modifiche al codice illustrate in precedenza evidenziate.

 [![Soluzione per iOS e Android](introduction-images/solution-sml.png "Struttura della soluzione DependencyService di esempio")](introduction-images/solution.png#lightbox "Struttura della soluzione DependencyService di esempio")

> [!NOTE]
> È **necessario** specificare un'implementazione in tutti i progetti della piattaforma. Se non viene registrata nessuna implementazione dell'interfaccia il `DependencyService` non sarà in grado di risolvere il metodo `Get<T>()` in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
