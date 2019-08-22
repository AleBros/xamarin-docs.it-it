---
title: Parte 4 - Gestione di più piattaforme
description: Questo documento descrive come gestire la divergenza dell'applicazione in base alla piattaforma o alla funzionalità. Illustra le dimensioni dello schermo, le metafore di navigazione, il tocco e i movimenti, le notifiche push e i paradigmi di interfaccia, ad esempio elenchi e tabulazioni.
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 993a455ff0d376f213c403a55df71b3ac52d0d4b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887665"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4 - Gestione di più piattaforme

## <a name="handling-platform-divergence-amp-features"></a>Gestione delle funzionalità &amp; di divergenza della piattaforma

La divergenza non è solo un problema "multipiattaforma"; i dispositivi nella piattaforma "stessa" hanno funzionalità diverse, in particolare la vasta gamma di dispositivi Android disponibili. Il più ovvio e Basic è la dimensione dello schermo, ma gli altri attributi del dispositivo possono variare e richiedere a un'applicazione di controllare determinate funzionalità e si comportano in modo diverso in base alla loro presenza (o assenza).

Ciò significa che tutte le applicazioni devono gestire la degradazione normale della funzionalità, altrimenti presentano un set di funzionalità non accattivante, il più basso comune. L'integrazione completa di Novell con gli SDK nativi di ogni piattaforma consente alle applicazioni di sfruttare le funzionalità specifiche della piattaforma, quindi è opportuno progettare le app per l'uso di tali funzionalità.

Vedere la documentazione sulle funzionalità della piattaforma per una panoramica delle differenze tra le piattaforme e le funzionalità.

## <a name="examples-of-platform-divergence"></a>Esempi di divergenza della piattaforma

### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementi fondamentali presenti tra le piattaforme

Esistono alcune caratteristiche delle applicazioni per dispositivi mobili universali.
Si tratta di concetti di livello più alto che in genere sono veri di tutti i dispositivi e possono quindi formare la base della progettazione dell'applicazione:

- Selezione delle funzioni tramite schede o menu
- Elenchi di dati e scorrimento
- Visualizzazioni singole dei dati
- Modifica di visualizzazioni singole dei dati
- Spostamento indietro

Quando si progetta il flusso dello schermo di alto livello, è possibile basare un'esperienza utente comune su questi concetti.

### <a name="platform-specific-attributes"></a>Attributi specifici della piattaforma

Oltre agli elementi di base presenti in tutte le piattaforme, è necessario risolvere le principali differenze della piattaforma nella progettazione. Potrebbe essere necessario prendere in considerazione (e scrivere codice in modo specifico per gestire) queste differenze:

- **Dimensioni dello schermo** : alcune piattaforme (ad esempio iOS e versioni precedenti Windows Phone) hanno dimensioni dello schermo standardizzate relativamente semplici da usare come destinazione. I dispositivi Android hanno un'ampia gamma di dimensioni dello schermo, che richiedono più impegno per supportare nell'applicazione.
- **Metafore di navigazione** : differenze tra le piattaforme (ad esempio, pulsante hardware "indietro", controllo dell'interfaccia utente di panorama "e all'interno di piattaforme (Android 2 e 4, iPhone rispetto a iPad).
- **Tastiere** : alcuni dispositivi Android hanno tastiere fisiche, mentre altri hanno solo una tastiera software. Il codice che rileva quando una tastiera morbida è nascosta parte della schermata deve essere sensibile a queste differenze.
- **Tocco e movimenti** : il supporto del sistema operativo per il riconoscimento dei movimenti varia, soprattutto nelle versioni precedenti di ogni sistema operativo. Le versioni precedenti di Android hanno un supporto molto limitato per le operazioni di tocco, vale a dire che il supporto di dispositivi meno recenti potrebbe richiedere codice separato
- **Notifiche push** : sono disponibili diverse funzionalità/implementazioni in ogni piattaforma, ad esempio Riquadri animati in Windows).

### <a name="device-specific-features"></a>Funzionalità specifiche del dispositivo

Determinare quali sono le funzionalità minime necessarie per l'applicazione. o quando decidere quali funzionalità aggiuntive sfruttare in ogni piattaforma. Il codice verrà richiesto per rilevare le funzionalità e disabilitare le funzionalità o offrire alternative, ad esempio un'alternativa alla posizione geografica può essere consentire all'utente di digitare un percorso o scegliere da una mappa:

- **Fotocamera** : la funzionalità differisce tra i dispositivi: alcuni dispositivi non hanno una fotocamera, altri hanno fotocamere frontali e posteriori. Alcune fotocamere sono in grado di registrare video.
- **Area geografica & Maps** : il supporto per il percorso GPS o Wi-Fi non è presente in tutti i dispositivi. Le app devono inoltre soddisfare i diversi livelli di precisione supportati da ogni metodo.
- **Accelerometro, giroscopio e bussola** : queste funzionalità sono spesso disponibili solo in una selezione di dispositivi in ogni piattaforma, quindi le app devono sempre fornire un fallback quando l'hardware non è supportato.
- **Twitter e Facebook** : solo ' built-in ' in iOS5 e iOS6 rispettivamente. Nelle versioni precedenti e in altre piattaforme sarà necessario fornire le proprie funzioni di autenticazione e l'interfaccia direttamente con l'API di ogni servizio.
- **NFC (Near Field Communications** ): solo su (alcuni) telefoni Android (al momento della stesura del documento).

## <a name="dealing-with-platform-divergence"></a>Gestione della divergenza della piattaforma

Esistono due diversi approcci per supportare più piattaforme dalla stessa codebase, ognuna con un proprio set di vantaggi e svantaggi.

- **Astrazione della piattaforma** : modello di facciata aziendale, fornisce un accesso unificato tra piattaforme ed estrae le specifiche implementazioni della piattaforma in una singola API unificata.
- **Implementazione divergente** : chiamata di funzionalità specifiche della piattaforma tramite implementazioni divergenti tramite strumenti di architettura quali interfacce ed ereditarietà o compilazione condizionale.

## <a name="platform-abstraction"></a>Astrazione della piattaforma

### <a name="class-abstraction"></a>Astrazione della classe

Utilizzando interfacce o classi di base definite nel codice condiviso e implementate o estese nei progetti specifici della piattaforma. La scrittura e l'estensione di codice condiviso con astrazioni di classi sono particolarmente adatte alle librerie di classi portabili, perché hanno un subset limitato del Framework disponibile e non possono contenere direttive del compilatore per supportare rami di codice specifici della piattaforma.

#### <a name="interfaces"></a>Interfacce

L'uso delle interfacce consente di implementare classi specifiche della piattaforma che possono essere ancora passate nelle librerie condivise per sfruttare i vantaggi del codice comune.

L'interfaccia viene definita nel codice condiviso e passata nella libreria condivisa come parametro o proprietà.

Le applicazioni specifiche della piattaforma possono implementare l'interfaccia e sfruttare al tempo stesso il codice condiviso per l'elaborazione.

 **Vantaggi**

L'implementazione può contenere codice specifico della piattaforma e anche fare riferimento a librerie esterne specifiche della piattaforma.

 **Svantaggi**

Necessità di creare e passare implementazioni nel codice condiviso. Se l'interfaccia viene usata all'interno del codice condiviso, viene passata attraverso più parametri del metodo o in caso contrario viene spostata attraverso la catena di chiamate. Se il codice condiviso usa un numero elevato di interfacce diverse, è necessario crearle e impostarle nel codice condiviso in un punto qualsiasi.

#### <a name="inheritance"></a>Ereditarietà

Il codice condiviso può implementare classi astratte o virtuali che possono essere estese in uno o più progetti specifici della piattaforma. Questa operazione è simile all'uso delle interfacce ma con un comportamento già implementato. Sono disponibili diversi punti di vista se le interfacce o l'ereditarietà rappresentano una scelta migliore: in C# particolare perché consente solo l'ereditarietà singola può determinare il modo in cui è possibile progettare le API in futuro. Usare l'ereditarietà con cautela.

I vantaggi e gli svantaggi delle interfacce si applicano ugualmente all'ereditarietà, con il vantaggio aggiuntivo che la classe di base può contenere un codice di implementazione (probabilmente un'intera implementazione indipendente dalla piattaforma che può essere facoltativamente estesa).

## <a name="xamarinforms"></a>Xamarin.Forms

Vedere la documentazione di [Novell. Forms](~/get-started/index.yml) .

### <a name="other-cross-platform-libraries"></a>Altre librerie multipiattaforma

Queste librerie offrono inoltre funzionalità multipiattaforma per C# gli sviluppatori:

- [**Novell. Essentials**](~/essentials/index.md) : API multipiattaforma per le funzionalità comuni.
- [**SkiaSharp**](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) : grafica 2D multipiattaforma.

## <a name="conditional-compilation"></a>Compilazione condizionale

Esistono situazioni in cui il codice condiviso dovrà comunque funzionare in modo diverso in ogni piattaforma, possibilmente accedendo a classi o funzionalità che si comportano in modo diverso. La compilazione condizionale funziona meglio con i progetti di asset condivisi, in cui viene fatto riferimento allo stesso file di origine in più progetti con simboli diversi definiti.

I progetti Novell definiscono `__MOBILE__` sempre ciò che è vero per i progetti di applicazioni iOS e Android. prendere nota del doppio carattere di sottolineatura pre e post-correzione su questi simboli.

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

#### <a name="ios"></a>iOS

Novell. iOS definisce `__IOS__` che è possibile usare per rilevare i dispositivi iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

Sono disponibili anche simboli specifici per l'espressione di controllo e la TV:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

Il codice che deve essere compilato solo in applicazioni Novell. Android può usare gli elementi seguenti

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Ogni versione dell'API definisce anche una nuova direttiva del compilatore, quindi il codice di questo tipo consente di aggiungere funzionalità se le API più recenti sono destinate. Ogni livello API include tutti i simboli di livello "Lower". Questa funzionalità non è molto utile per supportare più piattaforme. il `__ANDROID__` simbolo sarà in genere sufficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

Attualmente non è disponibile un simbolo predefinito per Novell. Mac, ma è possibile aggiungerne uno personalizzato nelle opzioni del progetto dell'app Mac **> compilare > compilatore** nella casella **Definisci simboli** oppure modificare il file con **estensione csproj** e aggiungerlo (ad esempio `__MAC__`).

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Usare `WINDOWS_UWP`. Non sono presenti caratteri di sottolineatura che racchiudono la stringa come i simboli della piattaforma Novell.

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>Uso della compilazione condizionale

Un esempio di caso di compilazione condizionale semplice è l'impostazione del percorso del file del database SQLite. Le tre piattaforme hanno requisiti leggermente diversi per specificare il percorso del file:

- **iOS** : Apple preferisce che i dati non utente siano posizionati in un percorso specifico (la directory della libreria), ma non esiste alcuna costante di sistema per questa directory. Il codice specifico della piattaforma è necessario per compilare il percorso corretto.
- **Android** : il percorso di sistema restituito `Environment.SpecialFolder.Personal` da è una posizione accettabile per archiviare il file di database.
- **Windows Phone** : il meccanismo di archiviazione isolata non consente di specificare un percorso completo, ma solo un percorso relativo e un nome file.
- **Piattaforma UWP (Universal Windows Platform)** : USA `Windows.Storage` le API.

Il codice seguente usa la compilazione condizionale per `DatabaseFilePath` assicurarsi che sia corretto per ogni piattaforma:

```csharp
public static string DatabaseFilePath {
        get {
    var filename = "TodoDatabase.db3";
#if SILVERLIGHT
    // Windows Phone 8
    var path = filename;
#else

#if __ANDROID__
    string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
#if __IOS__
        // we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
        // (they don't want non-user-generated data in Documents)
        string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
        string libraryPath = Path.Combine (documentsPath, "..", "Library");
#else
        // UWP
        string libraryPath = Windows.Storage.ApplicationData.Current.LocalFolder.Path;
#endif
#endif
        var path = Path.Combine (libraryPath, filename);
#endif
        return path;
}
```

Il risultato è una classe che può essere compilata e utilizzata in tutte le piattaforme, posizionando il file di database SQLite in un percorso diverso in ogni piattaforma.
