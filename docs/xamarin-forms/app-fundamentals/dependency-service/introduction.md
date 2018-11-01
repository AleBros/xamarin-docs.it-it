---
title: Introduzione a DependencyService
description: Questo articolo illustra il funzionamento della classe di xamarin. Forms DependencyService per accedere alle funzionalità di piattaforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675016"
---
# <a name="introduction-to-dependencyservice"></a>Introduzione a DependencyService

## <a name="overview"></a>Panoramica

[`DependencyService`](xref:Xamarin.Forms.DependencyService) consente alle App a chiamare funzionalità specifiche della piattaforma da codice condiviso. Questa funzionalità consente alle App xamarin. Forms di eseguire qualsiasi operazione che può eseguire un'app nativa.

`DependencyService` è un localizzatore di servizi. In pratica, viene definita un'interfaccia e `DependencyService` trova la corretta implementazione di tale interfaccia dai vari progetti di piattaforma.

> [!NOTE]
> Per impostazione predefinita, il [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) risolverà solo le implementazioni di piattaforma che dispongono di costruttori senza parametri. Tuttavia, un metodo di risoluzione delle dipendenze può essere inserito in xamarin. Forms che utilizza un contenitore di inserimento delle dipendenze o metodi factory per risolvere le implementazioni della piattaforma. Questo approccio può essere utilizzato per risolvere le implementazioni di piattaforma che dispongono di costruttori con parametri. Per altre informazioni, vedere [risoluzione delle dipendenze in xamarin. Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Come funziona DependencyService

App xamarin. Forms necessari quattro componenti da usare `DependencyService`:

- **Interfaccia** &ndash; la funzionalità necessaria è definita da un'interfaccia nel codice condiviso.
- **Implementazione per ogni piattaforma** &ndash; le classi che implementano l'interfaccia devono essere aggiunto a ogni progetto della piattaforma.
- **Registrazione** &ndash; ogni classe di implementazione deve essere registrato con `DependencyService` tramite un attributo di metadati. Abilita registrazione `DependencyService` per trovare la classe di implementazione e fornirlo al posto dell'interfaccia in fase di esecuzione.
- **Le chiamate a DependencyService** &ndash; condivise di codice dovrà chiamare in modo esplicito `DependencyService` per richiedere le implementazioni dell'interfaccia.

Si noti che è necessario fornire implementazioni per ogni progetto della piattaforma della soluzione. Progetti di piattaforma senza alcuna implementazione avrà esito negativo in fase di esecuzione.

La struttura dell'applicazione è illustrata nel diagramma seguente:

![](introduction-images/overview-diagram.png "Struttura dell'applicazione DependencyService")

### <a name="interface"></a>Interfaccia

L'interfaccia di che progettazione verrà definita l'interazione dell'utente con funzionalità specifiche della piattaforma. Prestare attenzione se si sta sviluppando un componente per essere condivisi così come un componente o un pacchetto NuGet. Progettazione di API possa stabilire o interrompere un pacchetto. L'esempio seguente specifica un'interfaccia semplice per pronunciare il testo che consente una flessibilità nello specificare le parole da pronunciare ma lascia l'implementazione per essere personalizzati per ogni piattaforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementazione per ogni piattaforma

Dopo aver sviluppata un'interfaccia appropriata, tale interfaccia deve essere implementata nel progetto per ogni piattaforma di destinazione. Ad esempio, la classe seguente implementa il `ITextToSpeech` interfaccia in iOS:

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

Riassumendo, l'implementazione specifica della piattaforma aspetto simile al seguente:

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

Nota: la registrazione eseguita a livello di spazio dei nomi, non a livello di classe.

#### <a name="universal-windows-platform-net-native-compilation"></a>Universal Windows Platform compilazione .NET Native

I progetti UWP che usano l'opzione di compilazione .NET Native devono seguire un [configurazione leggermente diverse](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) durante l'inizializzazione di xamarin. Forms. Compilazione .NET native richiede anche la registrazione leggermente diversa per servizi di dipendenza.

Nel **App.xaml.cs** del file, registrare manualmente ogni servizio di dipendenza definita nel progetto UWP usando la `Register<T>` metodo, come illustrato di seguito:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Nota: la registrazione manuale tramite `Register<T>` è efficace solo nella versione compilazioni con compilazione .NET Native. Se si omette questa riga, le compilazioni di Debug continuerà a funzionare, ma build di rilascio avrà esito negativo caricare il servizio di dipendenza.

### <a name="call-to-dependencyservice"></a>Le chiamate a DependencyService

Una volta che il progetto è stato impostato con le implementazioni per ogni piattaforma e un'interfaccia comune, usare `DependencyService` ottenere un'implementazione corretta in fase di esecuzione:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` troverà la corretta implementazione dell'interfaccia `T`.

### <a name="solution-structure"></a>Struttura della soluzione

Il [UsingDependencyService soluzione di esempio](https://developer.xamarin.com/samples/UsingDependencyService/) è illustrato di seguito per iOS e Android, con le modifiche al codice illustrati in precedenza evidenziato.

 [![iOS e Android soluzione](introduction-images/solution-sml.png "struttura della soluzione esempio DependencyService")](introduction-images/solution.png#lightbox "DependencyService esempio struttura della soluzione")

> [!NOTE]
> Si **necessario** fornire un'implementazione in tutti i progetti della piattaforma. Se nessuna implementazione dell'interfaccia è registrato, il `DependencyService` sarà in grado di risolvere il `Get<T>()` metodo in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
