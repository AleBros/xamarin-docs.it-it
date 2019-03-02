---
title: Parte 4 - Gestione di più piattaforme
description: Questo documento descrive come gestire divergenza di applicazioni basata su piattaforme o funzionalità. Viene descritto di dimensioni dello schermo, metafore di navigazione, tocco e i movimenti, le notifiche push e paradigmi di interfaccia, ad esempio gli elenchi e schede.
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 8fb373aa5081c8937da5110bad47c3f0decf0126
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197238"
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4 - Gestione di più piattaforme

## <a name="handling-platform-divergence-amp-features"></a>La gestione della piattaforma divergenza &amp; funzionalità

Divergenza non è semplicemente un problema di 'cross-platform'; i dispositivi nella piattaforma 'stesso' hanno funzionalità diverse (in particolare l'ampia gamma di dispositivi Android disponibili). Il modo più ovvio e base è dimensioni dello schermo, ma gli altri attributi del dispositivo possono variare e richiedono un'applicazione di controllo per alcune funzionalità e si comportano in base ai loro presenza (o l'assenza).

Ciò significa che tutte le applicazioni devono affrontare riduzione del funzionamento della funzionalità, altrimenti presentare un insieme di funzionalità minimo comune denominatore graficamente meno elegante. Integrazione completa di Xamarin con SDK nativi di tutte le piattaforme consentono alle applicazioni di sfruttare le funzionalità specifiche della piattaforma, quindi è opportuno progettare app di usare tali funzionalità.

Vedere la documentazione di funzionalità della piattaforma per una panoramica del modo in cui le piattaforme differiscono dalle funzionalità.

## <a name="examples-of-platform-divergence"></a>Esempi di divergenza di piattaforma

### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementi fondamentali presenti tra le piattaforme

Esistono alcune caratteristiche delle applicazioni per dispositivi mobili universali.
Questi sono i concetti di livello superiore che sono in genere accade per tutti i dispositivi e possono quindi costituire la base della progettazione dell'applicazione:

-  Selezione di funzionalità tramite le schede o i menu
-  Elenchi di dati e lo scorrimento
-  Sola visualizzazioni dei dati
-  Modifica di viste singole dei dati
-  Tornare indietro

Quando si progetta il flusso di alto livello dello schermo è possibile basare i un'esperienza utente comune su questi concetti.

### <a name="platform-specific-attributes"></a>Attributi specifici della piattaforma

Oltre agli elementi di base presenti in tutte le piattaforme, è necessario per le differenze principali della piattaforma di indirizzo nella progettazione. Potrebbe essere necessario prendere in considerazione (e scrivere codice in modo specifico per gestire) queste differenze:

-   **Dimensioni di schermi** – alcune piattaforme (ad esempio, iOS e le versioni precedenti di Windows Phone) usa le dimensioni dello schermo che sono relativamente semplici di destinazione. I dispositivi Android hanno un'ampia gamma di dimensioni dello schermo, che richiedono più complessa per il supporto nell'applicazione.
-   **Metafore navigazione** – differiscono tra le piattaforme (ad es. pulsante hardware 'indietro', controllo dell'interfaccia utente Panorama) e all'interno di piattaforme (Android 2 e 4, iPhone e iPad).
-   **Tastiere** : i dispositivi Android alcuni hanno tastiere fisiche mentre altri utenti hanno solo una tastiera software. Il codice che rileva se in una soft-tastiera copre parte della schermata deve essere sensibile a queste differenze.
-   **Tocco e movimenti** – varia in sistemi operativi supportati per il riconoscimento di movimento, soprattutto nelle versioni precedenti di ogni sistema operativo. Le versioni precedenti di Android hanno molto supporto limitato per le operazioni di tocco, vale a dire che i dispositivi meno recenti supportati potrebbe richiedere codice separato
-   **Le notifiche push** : sono disponibili diverse funzionalità/implementazioni in ogni piattaforma (ad es. Riquadri animati in Windows).

### <a name="device-specific-features"></a>Funzionalità specifiche del dispositivo

Determinare quali le funzionalità minime necessarie per l'applicazione devono essere; In alternativa, quando decidere quali funzionalità aggiuntive per sfruttare in ogni piattaforma. Sarà necessario codice per rilevare le funzionalità e disabilitare la funzionalità o offrono alternative (ad es. un'alternativa alla località geografica potrebbe essere che consente di digitare un percorso oppure sceglierne una mappa):

-   **Fotocamera** – funzionalità varia tra i dispositivi: alcuni dispositivi non hanno una fotocamera, altri dispongono di entrambe le telecamere anteriore e posteriore. Alcuni fotocamere sono in grado di registrare video.
-   **Posizione geografica & mapping** : supporto per la posizione GPS o Wi-Fi non è presente in tutti i dispositivi. Le app è inoltre necessitano tenere in considerazione per i diversi livelli di precisione supportato da ogni metodo.
-   **Accelerometro, il Giroscopio e compass** – queste funzionalità sono spesso disponibili in solo una selezione dei dispositivi in ogni piattaforma, in modo che le app devono quasi sempre fornire un fallback quando l'hardware non è supportato.
-   **Twitter e Facebook** : solo 'predefinita' su iOS5 e iOS6 rispettivamente. In altre piattaforme e versioni precedenti è necessario fornire funzioni di autenticazione e interfacciarsi direttamente con ogni API di servizi.
-   **Quasi campo Communications (NFC)** : solo in (alcuni) telefoni Android (al momento della stesura).

## <a name="dealing-with-platform-divergence"></a>Gestione di divergenza di piattaforma

Esistono due diversi approcci per supportare più piattaforme dalla stessa codebase, ognuno con un proprio set di vantaggi e svantaggi.

-   **Astrazione della piattaforma** : Schema facciata Business offre un accesso unificato tra le piattaforme e consente di astrarre le implementazioni della piattaforma particolare in un'API unificata.
-   **Implementazione divergenti** : chiamata di piattaforma specifica funzionalità tramite le implementazioni divergenti tramite gli strumenti di architettura, ad esempio compilazione condizionale o di ereditarietà e interfacce.

## <a name="platform-abstraction"></a>Astrazione della piattaforma

### <a name="class-abstraction"></a>Astrazione della classe

Usando interfacce o classi di base definito nel codice condiviso e implementate o estese in progetti specifici delle piattaforme. La scrittura e l'estensione di codice condiviso di astrazioni di classe è particolarmente adatta alle librerie di classi portabile perché hanno un subset limitato di framework disponibili e non può contenere le direttive del compilatore per supportare i rami di codice specifico della piattaforma.

#### <a name="interfaces"></a>Interfacce

Uso di interfacce consente di implementare classi specifiche della piattaforma che possono ancora essere passate nelle librerie condivise per sfruttare il codice comune.

L'interfaccia è definita nel codice condiviso e passata nella libreria condivisa come un parametro o una proprietà.

Le applicazioni specifiche della piattaforma possono quindi implementare l'interfaccia e comunque sfruttare il codice condiviso per 'elaborarli'.

 **Vantaggi**

L'implementazione può contenere codice specifico della piattaforma e anche fare riferimento alle librerie esterne specifico della piattaforma.

 **Svantaggi**

Necessità di creare e passare le implementazioni il codice condiviso. Se l'interfaccia viene usata nel codice condiviso quindi finiscono per passare più parametri di metodo o in caso contrario propagato attraverso la catena di chiamate. Se il codice condiviso che utilizzano grandi quantità di interfacce diverse quindi sono devono tutte essere creati e impostati nel codice condiviso in un punto.

#### <a name="inheritance"></a>Ereditarietà

Il codice condiviso è stato possibile implementare classi astratte o virtuale che può essere estesa in uno o più progetti specifici della piattaforma. Questa operazione è simile all'uso di interfacce, ma con un comportamento già implementata. Esistono diversi punti di vista se interfacce o un'ereditarietà sono una scelta di progettazione migliore: in particolare perché c# consente solo l'ereditarietà singola è possibile imporre il modo in cui le API possono essere progettate in futuro. Utilizzare l'ereditarietà con cautela.

I vantaggi e svantaggi delle interfacce sono ugualmente applicabili a ereditarietà, con l'ulteriore vantaggio di classe di base può contenere un codice di implementazione (probabilmente un'intera piattaforma agnostico implementazione che può essere facoltativamente estesi).

## <a name="xamarinforms"></a>Xamarin.Forms

Vedere le [xamarin. Forms](~/get-started/index.yml) documentazione.

### <a name="other-cross-platform-libraries"></a>Altre librerie multipiattaforma

Queste librerie offrono anche funzionalità di multi-piattaforma per C# gli sviluppatori:

- [**Xamarin.Essentials** ](~/essentials/index.md) – APIs multipiattaforma per le funzionalità comuni.
- [**SkiaSharp** ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) : grafica 2D multipiattaforma.

## <a name="conditional-compilation"></a>Compilazione condizionale

Esistono alcune situazioni in cui il codice condiviso dovranno comunque funzionare in modo diverso in ogni piattaforma, possibilmente l'accesso a classi o funzionalità che si comportano in modo diverso. Compilazione condizionale funziona meglio con progetti Asset condivisi, in cui viene fatto riferimento stesso file di origine in più progetti con diversi simboli definiti.

Progetti Xamarin è sempre necessario definiscono `__MOBILE__` che è true per iOS e i progetti applicazione Android (si noti il doppio carattere di sottolineatura pre-elaborazione e post-correzione in questi simboli).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```
#### <a name="ios"></a>iOS

Xamarin. IOS definisce `__IOS__` che è possibile usare per rilevare i dispositivi iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

Sono inoltre disponibili i simboli specifici di espressioni di controllo e programmi TV:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

#### <a name="android"></a>Android

Il codice che deve essere compilato solo nelle applicazioni xamarin. Android può usare il seguente

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Ogni versione dell'API definisce anche una nuova direttiva del compilatore, in modo da codice simile al seguente in questo modo è aggiungere funzionalità se vengono considerati come destinazione le API più recenti. Ogni livello API include tutti i simboli di livello 'inferiori'. Questa funzionalità non è particolarmente utile per il supporto di più piattaforme; in genere il `__ANDROID__` simbolo sarà sufficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

#### <a name="mac"></a>Mac

Non esiste attualmente un simbolo incorporato per xamarin. Mac, ma è possibile aggiungere un progetto di app nel Mac **Opzioni > compilazione > compilatore** nel **definiscono i simboli** , o modificarne la **csproj**  file e aggiungere presente (ad esempio `__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

#### <a name="universal-windows-platform-uwp"></a>Piattaforma UWP (Universal Windows Platform)

Usare `WINDOWS_UWP`. Sono non disponibili caratteri di sottolineatura che circondano la stringa, ad esempio i simboli di piattaforma Xamarin.

```csharp
#if WINDOWS_UWP
// UWP-specific code
#endif
```

#### <a name="using-conditional-compilation"></a>Utilizza la compilazione condizionale

Un semplice esempio case study di compilazione condizionale è l'impostazione il percorso del file per il file di database SQLite. Tre piattaforme presentano requisiti leggermente diversi per specificare il percorso del file:

-   **iOS** : Apple preferenziale per dati non utente da inserire in un percorso specifico (la directory di libreria), ma non vi è alcuna costante di sistema per questa directory. Codice specifico della piattaforma è necessario per compilare il percorso corretto.
-   **Android** : il percorso di sistema restituito da `Environment.SpecialFolder.Personal` è un percorso accettabile per archiviare il file di database.
-   **Windows Phone** – il meccanismo di memorizzazione isolato non supporta un percorso completo, specificare solo un percorso relativo e nome file.
-   **Piattaforma Windows Universal** – utilizza `Windows.Storage` API.

Il codice seguente usa la compilazione condizionale per garantire la `DatabaseFilePath` sia corretto per ogni piattaforma:

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

Il risultato è una classe che può essere compilata e usata in tutte le piattaforme, inserendo il file di database SQLite in un percorso diverso in ogni piattaforma.
