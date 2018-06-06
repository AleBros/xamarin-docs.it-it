---
title: Introduzione a DependencyService
description: Comprendere il funzionamento di DependencyService per accedere alle funzionalità di piattaforma nativa
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 6a0544a58245efed696b92c543fbaf5e7e1b8365
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732827"
---
# <a name="introduction-to-dependencyservice"></a>Introduzione a DependencyService

## <a name="overview"></a>Panoramica

[`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) consente alle app di chiamare funzionalità specifiche della piattaforma da codice condiviso. Questa funzionalità consente alle App xamarin. Forms di operazioni che è possibile eseguire un'applicazione nativa.

`DependencyService` è un resolver di dipendenza. In pratica, viene definita un'interfaccia e `DependencyService` consente di individuare la corretta implementazione di tale interfaccia dai vari progetti di piattaforma.

## <a name="how-dependencyservice-works"></a>Funzionamento di DependencyService

Le app xamarin. Forms necessario quattro componenti da usare `DependencyService`:

- **Interfaccia** &ndash; è definita la funzionalità richiesta da un'interfaccia nel codice condiviso.
- **Implementazione per ogni piattaforma** &ndash; classi che implementano l'interfaccia devono essere aggiunto a ogni progetto di piattaforma.
- **Registrazione** &ndash; ogni classe di implementazione deve essere registrato con `DependencyService` tramite un attributo di metadati. Abilita registrazione `DependencyService` per trovare la classe di implementazione e fornirlo al posto dell'interfaccia in fase di esecuzione.
- **Chiamata a DependencyService** &ndash; condiviso codice deve chiamare in modo esplicito `DependencyService` per richiedere per le implementazioni dell'interfaccia.

Si noti che le implementazioni devono essere fornite per ogni progetto della piattaforma della soluzione. Progetti di piattaforma senza implementazioni avrà esito negativo in fase di esecuzione.

La struttura dell'applicazione è illustrata nel diagramma seguente:

![](introduction-images/overview-diagram.png "Struttura dell'applicazione DependencyService")

### <a name="interface"></a>Interfaccia

L'interfaccia di progettazione definirà modalità di interazione con funzionalità specifiche della piattaforma. Prestare attenzione se si sviluppa un componente per la condivisione come un componente o un pacchetto Nuget. Progettazione delle API possa stabilire o interrompere un pacchetto. Nell'esempio seguente specifica un'interfaccia semplice per parlare di testo che consente una flessibilità nello specificare le parole da leggere, ma lascia l'implementazione per essere personalizzati per ogni piattaforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementazione per ogni piattaforma

Una volta un'interfaccia adatta è stata progettata, tale interfaccia deve essere implementata nel progetto per ogni piattaforma di destinazione. Ad esempio, la classe seguente implementa il `ITextToSpeech` interfaccia in iOS:

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

Ogni implementazione dell'interfaccia deve essere registrata con `DependencyService` con un attributo di metadati. Il codice seguente viene registrata l'implementazione per iOS:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

Riassumendo, l'implementazione specifica della piattaforma è simile al seguente:

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

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilazione nativa .NET di piattaforma Windows universale

I progetti UWP che usano l'opzione di compilazione .NET Native devono seguire un [configurazione leggermente diversa](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) durante l'inizializzazione di xamarin. Forms. Compilazione .NET native richiede inoltre registrazione leggermente diversa per servizi di dipendenza.

Nel **App.xaml.cs** file, registrare manualmente ogni servizio di dipendenza definita nel progetto UWP usando il `Register<T>` (metodo), come illustrato di seguito:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Nota: la registrazione manuale utilizzando `Register<T>` è efficace solo nella versione le compilazioni usando compilazione .NET Native. Se si omette questa riga, le compilazioni di Debug continuerà a funzionare, ma build di rilascio avrà esito negativo caricare il servizio di dipendenza.

### <a name="call-to-dependencyservice"></a>Chiamata a DependencyService

Una volta il progetto è stato configurato con un'interfaccia comune e le implementazioni per ogni piattaforma, utilizzare `DependencyService` per ottenere l'implementazione di destra in fase di esecuzione:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` troverà la corretta implementazione dell'interfaccia `T`.

### <a name="solution-structure"></a>Struttura della soluzione

Il [UsingDependencyService soluzione di esempio](https://developer.xamarin.com/samples/UsingDependencyService/) è illustrato di seguito per iOS e Android, con le modifiche al codice illustrati in precedenza evidenziata.

 [![iOS e Android soluzione](introduction-images/solution-sml.png "struttura soluzione di esempio DependencyService")](introduction-images/solution.png#lightbox "DependencyService struttura di soluzione di esempio")

> [!NOTE]
> Si **deve** fornire un'implementazione in ogni progetto della piattaforma. Se non viene registrata alcuna implementazione dell'interfaccia, il `DependencyService` sarà in grado di risolvere il `Get<T>()` metodo in fase di esecuzione.


## <a name="related-links"></a>Collegamenti correlati

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
