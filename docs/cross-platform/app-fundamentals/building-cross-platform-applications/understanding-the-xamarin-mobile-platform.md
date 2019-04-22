---
title: 'Parte 1: informazioni sulla piattaforma per dispositivi mobili Xamarin'
description: Questo documento descrive la piattaforma Xamarin a livello generale, esamina il processo di compilazione, l'accesso di platform SDK, la condivisione del codice, creazione di un'interfaccia utente, finestre di progettazione visiva e altro.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f5008d4986baa0575030e077b66b69ec0a4fad00
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854418"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1: informazioni sulla piattaforma per dispositivi mobili Xamarin

La piattaforma Xamarin è costituito da un numero di elementi che consentono di sviluppare applicazioni per iOS e Android:

-   **C#linguaggio** : consente di usare una sintassi familiare e funzionalità sofisticate come Generics, LINQ e la libreria Task Parallel Library.
-   **.NET framework mono** – fornisce un'implementazione multipiattaforma di funzionalità complete in Microsoft .NET framework.
-   **Compilatore** : a seconda della piattaforma, produce un'app nativa (ad es. per iOS) o un'applicazione .NET integrata e runtime (ad es. Android). Il compilatore esegue anche numerose ottimizzazioni per la distribuzione per dispositivi mobili, ad esempio il collegamento di stoccaggio codice non usato.
-   **Gli strumenti dell'IDE** – The Visual Studio su Mac e Windows consente di creare, compilare e distribuire i progetti Xamarin.

Inoltre, poiché la lingua sottostante è C# con .NET framework, i progetti possono essere strutturati per condividere il codice che può essere distribuito anche in Windows Phone.

## <a name="under-the-hood"></a>Dietro le quinte

Sebbene Xamarin consente di scrivere le app C#e condividono lo stesso codice su più piattaforme, l'implementazione effettiva in ogni sistema è molto diverso.

## <a name="compilation"></a>Compilazione

Il C# origine trasforma il modo in un'app nativa in modi molto diversi in ogni piattaforma:

-   **iOS** – C# è ahead of time (AOT) compilate in linguaggio assembly ARM. .NET framework è incluso, le classi non usate vengono rimosse durante il collegamento per ridurre le dimensioni dell'applicazione. Apple non consente la generazione del codice runtime su iOS, in modo che non sono disponibili alcune funzionalità del linguaggio (vedere [limitazioni di xamarin. IOS](~/ios/internals/limitations.md) ).
-   **Android** – C# viene compilata a livello di integrità e incluso nel pacchetto con MonoVM + JIT'ing. Classi non usate in framework vengono rimosse durante il collegamento. L'applicazione viene eseguito side-by-side con Java/ART (runtime di Android) e interagisce con i tipi nativi tramite JNI (vedere [xamarin. Android le limitazioni](~/android/internals/limitations.md) ).
-   **Windows** – C# viene compilato a livello di integrità eseguite dal runtime incorporato e non richiede gli strumenti di Xamarin. Progettazione di applicazioni Windows linee guida di Xamarin seguente rende più semplice riutilizzare il codice in iOS e Android.
  Si noti che la piattaforma universale di Windows ha anche un **.NET Native** opzione che si comporta in modo analogo alla compilazione AOT di xamarin. IOS.


La documentazione del linker [xamarin. IOS](~/ios/deploy-test/linker.md) e [xamarin. Android](~/android/deploy-test/linker.md) vengono fornite ulteriori informazioni relative a questa parte del processo di compilazione.

Runtime 'compilation: la generazione di codice in modo dinamico con `System.Reflection.Emit` : deve essere evitata.

Kernel di Apple impedisce la generazione dinamica del codice nei dispositivi iOS, pertanto la creazione di codice su immediatamente non funzionerà in xamarin. IOS. Analogamente, le funzionalità di Dynamic Language Runtime non sono utilizzabile con gli strumenti di Xamarin.

Alcune funzionalità di reflection disponibili (ad es. Monotouch. Dialog usarlo per l'API di Reflection), ma non la generazione di codice.

## <a name="platform-sdk-access"></a>Accesso di Platform SDK

Xamarin consente le funzionalità fornite dal SDK specifici della piattaforma facilmente accessibili con familiare C# sintassi:

-   **iOS** – xamarin. IOS espone Framework CocoaTouch SDK di Apple come spazi dei nomi che è possibile fare riferimento da C#. Ad esempio il framework UIKit che contiene tutti i controlli dell'interfaccia utente può essere incluso con un semplice `using UIKit;` istruzione.
-   **Android** – xamarin. Android espone Android SDK di Google come spazi dei nomi, è possibile fare riferimento a qualsiasi parte del SDK supportati con un utilizzo istruzione, ad esempio `using Android.Views;` per accedere ai controlli dell'interfaccia utente.
-   **Windows** : le app di Windows vengono compilate usando Visual Studio in Windows. Tipi di progetto includono Windows Form, WPF, WinRT e Universal Windows Platform (UWP).

## <a name="seamless-integration-for-developers"></a>Integrazione senza problemi per gli sviluppatori

La bellezza di Xamarin è che nonostante le differenze dietro le quinte, xamarin. IOS e xamarin. Android (accoppiato con Windows SDK Microsoft) offrono un'esperienza senza problemi per la scrittura C# il codice che possa essere riutilizzato in tutti e tre le piattaforme.

La logica di business, utilizzo del database, l'accesso alla rete e altre comuni funzioni possono essere scritte una sola volta e riutilizzate in ogni piattaforma, fornire una base per le interfacce utente specifiche della piattaforma che osservare ed eseguono come applicazioni native.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilità di sviluppo integrato (IDE) di ambiente

È possibile lo sviluppo con Xamarin in Visual Studio in Mac o Windows. L'IDE scelto verrà determinata dalle piattaforme di destinazione desiderato.

Poiché è possibile sviluppare app di Windows solo in Windows, per la compilazione per iOS, Android, _e_ Windows richiede Visual Studio per Windows. Tuttavia è possibile condividere i progetti e i file tra i computer Mac e Windows, in modo che App per iOS e Android può essere compilata in un computer Mac e codice condiviso in un secondo momento può essere aggiunto a un progetto di Windows.

I requisiti di sviluppo per ogni piattaforma sono descritti più dettagliatamente la [requisito](~/cross-platform/get-started/requirements.md) Guida.

### <a name="ios"></a>iOS

Lo sviluppo di applicazioni iOS richiede un computer Mac che eseguono macOS. È anche possibile usare Visual Studio per scrivere e distribuire applicazioni iOS con Xamarin in Visual Studio. Tuttavia, un computer Mac è ancora necessario per scopi di gestione delle licenze e compilazione.

Deve essere installato Xcode IDE di Apple per fornire il compilatore e il simulatore per il test. È possibile testare in your own Device [gratuitamente](~/ios/get-started/installation/device-provisioning/free-provisioning.md), ma per compilare applicazioni per la distribuzione (ad es. l'App di Store) è necessario aggiungere il programma per sviluppatori di Apple ($99 USD all'anno). Ogni volta che si invia o si aggiorna un'applicazione, debba essere esaminato e approvato da Apple prima di renderlo disponibile per il download.

Codice è scritto con l'IDE di Visual Studio e i layout di schermata possono essere compilati a livello di codice o modificati di Xamarin IOS Designer nell'IDE.

Vedere le [Guida all'installazione di xamarin. IOS](~/ios/get-started/installation/index.md) per istruzioni dettagliate sulla configurazione.

### <a name="android"></a>Android

Sviluppo di applicazioni Android richiede Java e Android SDK da installare. Questi fornire al compilatore, emulatori e altri strumenti necessari per la compilazione, distribuzione e test. Strumenti Java di Google Android SDK e di Xamarin possono tutti essere installati ed eseguiti in Windows e macOS. Sono consigliate le configurazioni seguenti:

- Windows 10 con Visual Studio 2019
- macOS Mojave (10.11 +) con Visual Studio 2019 per Mac

Xamarin offre un programma di installazione unificata che configurerà il sistema con il prerequisito Java, Android e Xamarin strumenti (tra cui una finestra di progettazione visiva per layout dello schermo). Vedere le [Guida all'installazione di xamarin. Android](~/android/get-started/installation/index.md) per istruzioni dettagliate.

È possibile compilare e testare applicazioni in un dispositivo reale senza alcuna licenza da Google, tuttavia per distribuire l'applicazione attraverso un archivio (ad esempio Google Play, Amazon o Barnes &amp; Noble) una tariffa di registrazione può essere dovuta all'operatore. Google Play pubblicherà l'app immediatamente, mentre altri archivi di dispongono di un processo di approvazione simile a Apple.

### <a name="windows"></a>WINDOWS

Le app di Windows (Windows Form, WPF o UWP) vengono compilate con Visual Studio. Non usano direttamente Xamarin. Tuttavia, C# codice può essere condivisi tra Windows, iOS e Android.
Visita Microsoft [Dev Center](https://developer.microsoft.com/) per conoscere gli strumenti necessari per lo sviluppo di Windows.

## <a name="creating-the-user-interface-ui"></a>Creazione dell'interfaccia utente

Dei vantaggi principali dell'uso di Xamarin è che l'interfaccia utente dell'applicazione utilizza i controlli nativi in ciascuna piattaforma, creazione di App che non sono distinguibili da un'applicazione scritta in Objective-C o Java (per iOS e Android rispettivamente).

Durante la creazione di schermate nell'app, è possibile disporre i controlli nel codice o creare schermate completate usando gli strumenti di progettazione disponibili per ogni piattaforma.

### <a name="create-controls-programmatically"></a>Creare i controlli a livello di codice

Ogni piattaforma consente a controlli dell'interfaccia da aggiungere a una schermata tramite codice utente. Può trattarsi di richiede molto tempo poiché può essere difficile identificare la progettazione terminata quando impostare come hardcoded pixel le coordinate per le posizioni di controllo e dimensioni.

Creazione di controlli a livello di codice è necessario che i vantaggi, tuttavia, in particolare in iOS per la creazione di viste che ridimensionare o eseguire il rendering in modo diverso tra le dimensioni dello schermo di iPhone e iPad.

### <a name="visual-designer"></a>Finestra di progettazione visiva

Ogni piattaforma ha un metodo diverso per la disposizione visiva schermate:

- **iOS** – iOS di Xamarin Designer semplifica la creazione di viste utilizzando i campi di proprietà e funzionalità di trascinamento e rilascio. Collettivamente costituiscono uno Storyboard, queste visualizzazioni ed è possibile accedervi nel **. Storyboard** file incluso nel progetto.
- **Android** : Xamarin offre una progettazione di interfaccia utente di trascinamento e rilascio Android per Visual Studio. Layout dello schermo Android vengono salvati come **. AXML** file quando si usano gli strumenti di Xamarin.
- **Windows** – Microsoft fornisce una progettazione di interfaccia utente di trascinamento e rilascio in Visual Studio e Blend. Come vengono archiviati i layout di schermata. File XAML.

Questi screenshot mostrano le finestre di progettazione schermata visual disponibili in ogni piattaforma:

 [ ![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "Questi screenshot mostrano le finestre di progettazione schermata visual disponibili in ogni piattaforma")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

In tutti i casi gli elementi che si crea in modo visivo possono farvi riferimento nel codice.

### <a name="user-interface-considerations"></a>Considerazioni sull'interfaccia utente

Dei vantaggi principali dell'uso di Xamarin per compilare applicazioni multipiattaforma è che è possibile sfruttare i vantaggi di Toolkit dell'interfaccia utente nativo per presentare un'interfaccia familiare all'utente. L'interfaccia utente eseguirà anche la stessa velocità come qualsiasi altra applicazione nativa.

Alcuni metafore dell'interfaccia utente di lavoro tra più piattaforme (ad esempio, tutte e tre le piattaforme usano un controllo elenco lo scorrimento simile), ma nell'ordine dall'applicazione per 'difficoltà nell'utilizzare' l'interfaccia utente deve sfruttare i vantaggi dell'utente specifiche della piattaforma gli elementi dell'interfaccia quando appropriato. Esempi di metafore dell'interfaccia utente specifiche della piattaforma:

- **iOS** : navigazione gerarchica con pulsante Indietro soft, schede nella parte inferiore della schermata.
- **Android** hardware/sistema software, eseguire il pulsante, dal menu azione, le schede nella parte superiore della schermata.
- **Windows** : app di Windows possono essere eseguite sul desktop, Tablet (ad esempio Microsoft Surface) e telefoni. I dispositivi Windows 10 possono disporre di hardware pulsante Indietro e i riquadri animati, ad esempio.

È consigliabile leggere le linee guida di progettazione rilevanti per le piattaforme di destinazione:

- **iOS** – [Human Interface Guidelines di Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** – [linee guida dell'interfaccia utente di Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** – [User Experience Design Guidelines for Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>Libreria e riutilizzo del codice

La piattaforma Xamarin consente di utilizzare nuovamente esistente C# codice tra tutte le piattaforme, nonché l'integrazione di librerie scritte in modo nativo per ogni piattaforma.

### <a name="c-source-and-libraries"></a>C#Origine e librerie

Perché usano i prodotti Xamarin C# e .NET framework, una grande quantità di codice sorgente esistente (entrambi aprire progetti interni e origine) può essere riusata in progetti xamarin. IOS o xamarin. Android. Spesso l'origine può semplicemente essere aggiunto a una soluzione Xamarin e funzionerà immediatamente. Se è stata utilizzata una funzionalità non supportata di .NET framework, alcune modifiche potrebbero essere necessari.

Esempi di C# origine che può essere utilizzati in xamarin. IOS o xamarin. Android includono: SQLite-NET, newtonsoft. JSON e SharpZipLib.

### <a name="objective-c-bindings--binding-projects"></a>Binding Objective-C + progetti di Binding

Xamarin offre uno strumento denominato *btouch* che facilita la creazione di associazioni che consentono di librerie Objective-C da utilizzare nei progetti xamarin. IOS. Vedere il [documentazione sui tipi Objective-C di associazione](~/cross-platform/macios/binding/binding-types-reference.md) per informazioni dettagliate sul modo in cui questa operazione viene eseguita.

Esempi di librerie Objective-C che possono essere usate in xamarin. ios: Codice a barre RedLaser l'analisi, l'integrazione di PayPal e Google Analitica. Binding xamarin. IOS open source sono disponibili nel [github](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>le associazioni con estensione jar + progetti di Binding

Xamarin supporta l'uso di librerie Java esistenti in xamarin. Android. Vedere il [associazione una documentazione della libreria Java](~/android/platform/binding-java-library/index.md) per informazioni dettagliate su come usare un. File JAR da xamarin. Android.

Binding xamarin. Android open source sono disponibili nel [github](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C tramite PInvoke

La tecnologia "Platform Invoke" (P/Invoke) consente al codice gestito (C#) per chiamare metodi nelle librerie native, nonché supporto per le librerie native richiamare il codice gestito.

Ad esempio, il [SQLite-NET](https://github.com/praeclarum/sqlite-net) libreria Usa istruzioni simile al seguente:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Ciò consente di associare all'implementazione del linguaggio C SQLite nativo in iOS e Android.
Gli sviluppatori esperti in un'API C esistente è possono costruire un set di C# classi di eseguire il mapping all'API nativa e usare il codice di piattaforma esistente. Documentazione per [collegamento di librerie native](~/ios/platform/native-interop.md) in xamarin. IOS, principi analoghi si applicano a xamarin. Android.

### <a name="c-via-cppsharp"></a>C++ tramite CppSharp

Miguel spiega CXXI (ora denominato [CppSharp](https://github.com/mono/CppSharp)) sul suo [blog](https://tirania.org/blog/archive/2011/Dec-19.html). Un'alternativa all'associazione direttamente a una libreria di C++ consiste nel creare un wrapper di C ed effettuare l'associazione tramite P/Invoke.
