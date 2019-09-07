---
title: 'Parte 1: informazioni sulla piattaforma Novell mobile'
description: Questo documento descrive la piattaforma Novell a un livello elevato, esaminando il processo di compilazione, l'accesso Platform SDK, la condivisione del codice, la creazione dell'interfaccia utente, le finestre di progettazione visiva e altro ancora.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: af2b8cd39d5fb1b0ce6c12f7d6ad87e245b9a594
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761964"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1: informazioni sulla piattaforma Novell mobile

La piattaforma Novell è costituita da una serie di elementi che consentono di sviluppare applicazioni per iOS e Android:

- lingua: consente di usare una sintassi familiare e funzionalità sofisticate come generics, LINQ e la libreria di attività parallela. **C#**
- **Mono .NET Framework** : fornisce un'implementazione multipiattaforma delle funzionalità complete di Microsoft .NET Framework.
- **Compilatore** : a seconda della piattaforma, produce un'app nativa, ad esempio iOS) o un'applicazione .NET e un runtime integrati (ad esempio Android). Il compilatore esegue anche molte ottimizzazioni per la distribuzione di dispositivi mobili, ad esempio il collegamento di codice non usato.
- **Strumenti IDE** : Visual Studio in Mac e Windows consente di creare, compilare e distribuire progetti Novell.

Inoltre, poiché il linguaggio sottostante è C# con .NET Framework, i progetti possono essere strutturati in modo da condividere il codice che può essere distribuito anche in Windows Phone.

## <a name="under-the-hood"></a>Dietro le quinte

Sebbene Novell consenta di scrivere app in C#e condividere lo stesso codice su più piattaforme, l'implementazione effettiva in ogni sistema è molto diversa.

## <a name="compilation"></a>Compilazione

Il C# codice sorgente è in grado di accedere a un'app nativa in modi molto diversi in ogni piattaforma:

- **iOS** : C# è stata compilata come AOT (Ahead of Time) in linguaggio assembly ARM. .NET Framework è incluso e le classi inutilizzate vengono rimosse durante il collegamento per ridurre le dimensioni dell'applicazione. Apple non consente la generazione del codice di runtime in iOS, quindi alcune funzionalità del linguaggio non sono disponibili (vedere [limitazioni di Novell. iOS](~/ios/internals/limitations.md) ).
- **Android** : C# viene compilato in il e incluso in un pacchetto con MonoVM + JIT'ing. Le classi inutilizzate nel Framework vengono rimosse durante il collegamento. L'applicazione viene eseguita side-by-side con Java/ART (runtime di Android) e interagisce con i tipi nativi tramite JNI (vedere [limitazioni di Novell. Android](~/android/internals/limitations.md) ).
- **Windows** : C# viene compilato in il e viene eseguito dal runtime predefinito e non richiede gli strumenti Novell. La progettazione di applicazioni Windows in base alle linee guida di Novell rende più semplice riusare il codice in iOS e Android.
  Si noti che il piattaforma UWP (Universal Windows Platform) dispone anche di un'opzione di **.NET native** che si comporta in modo analogo alla compilazione AOT di Novell. iOS.

La documentazione del linker per [Novell. iOS](~/ios/deploy-test/linker.md) e [Novell. Android](~/android/deploy-test/linker.md) fornisce ulteriori informazioni su questa parte del processo di compilazione.

' Compilazione ' del runtime: è consigliabile evitare la `System.Reflection.Emit` generazione dinamica di codice con.

Il kernel di Apple impedisce la generazione di codice dinamico nei dispositivi iOS, di conseguenza l'emissione di codice in tempo reale non funzionerà in Novell. iOS. Analogamente, le funzionalità di Dynamic Language Runtime non possono essere usate con gli strumenti Novell.

Alcune funzionalità di Reflection funzionano, ad esempio MonoTouch. la finestra di dialogo la usa per l'API di reflection, ma non per la generazione di codice.

## <a name="platform-sdk-access"></a>Accesso a Platform SDK

Novell rende facilmente accessibili le funzionalità fornite dall'SDK specifico della piattaforma con una sintassi C# familiare:

- **iOS** : Novell. iOS espone i Framework SDK CocoaTouch di Apple come spazi dei nomi a cui è possibile C#fare riferimento. Ad esempio, il Framework UIKit che contiene tutti i controlli dell'interfaccia utente può essere incluso in `using UIKit;` un'istruzione semplice.
- **Android** : Novell. Android espone Google Android SDK come spazi dei nomi, quindi è possibile fare riferimento a qualsiasi parte dell'SDK supportato con un'istruzione using, ad `using Android.Views;` esempio per accedere ai controlli dell'interfaccia utente.
- **Windows** : le app di Windows sono compilate con Visual Studio in Windows. I tipi di progetto includono Windows Forms, WPF, WinRT e il piattaforma UWP (Universal Windows Platform) (UWP).

## <a name="seamless-integration-for-developers"></a>Perfetta integrazione per gli sviluppatori

La bellezza di Novell è che, nonostante le differenze con la cappa, Novell. iOS e Novell. Android (abbinato a Microsoft Windows SDK) offrono un'esperienza uniforme per C# la scrittura di codice che può essere riutilizzato in tutte e tre le piattaforme.

La logica di business, l'utilizzo del database, l'accesso alla rete e altre funzioni comuni possono essere scritte una sola volta e riutilizzate in ogni piattaforma, fornendo una base per le interfacce utente specifiche della piattaforma che appaiono ed eseguono come applicazioni native.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilità dell'ambiente di sviluppo integrato (IDE)

Lo sviluppo Novell può essere eseguito in Visual Studio in Mac o Windows. L'IDE scelto sarà determinato dalle piattaforme a cui si vuole fare riferimento.

Poiché le app di Windows possono essere sviluppate solo in Windows, per la compilazione per iOS, Android _e_ Windows è necessario Visual Studio per Windows. Tuttavia è possibile condividere progetti e file tra computer Windows e Mac, in modo che le app iOS e Android possano essere compilate in un Mac e il codice condiviso possa essere aggiunto in un secondo momento a un progetto Windows.

I requisiti di sviluppo per ogni piattaforma vengono descritti in modo più dettagliato nella Guida ai [requisiti](~/cross-platform/get-started/requirements.md) .

### <a name="ios"></a>iOS

Per lo sviluppo di applicazioni iOS è necessario un computer Mac che esegue macOS. È anche possibile usare Visual Studio per scrivere e distribuire applicazioni iOS con Novell in Visual Studio. Tuttavia, un Mac è ancora necessario per scopi di compilazione e di gestione delle licenze.

È necessario installare l'IDE di Apple Xcode per fornire il compilatore e il simulatore per il test. Puoi testare [gratuitamente](~/ios/get-started/installation/device-provisioning/free-provisioning.md)i tuoi dispositivi, ma per creare applicazioni per la distribuzione (ad esempio, App Store) è necessario partecipare al programma per sviluppatori di Apple ($99 USD all'anno). Ogni volta che si invia o si aggiorna un'applicazione, è necessario esaminarla e approvarla da Apple prima di renderla disponibile per il download da un cliente.

Il codice viene scritto con l'IDE di Visual Studio e i layout dello schermo possono essere compilati a livello di programmazione o modificati con la finestra di progettazione iOS di Novell in uno dei due IDE.

Vedere la [Guida all'installazione di Novell. iOS](~/ios/get-started/installation/index.md) per istruzioni dettagliate su come eseguire la configurazione.

### <a name="android"></a>Android

Per lo sviluppo di applicazioni Android è necessario che siano installati gli SDK Java e Android. Che forniscono il compilatore, l'emulatore e altri strumenti necessari per la compilazione, la distribuzione e il testing. Java, Google Android SDK e gli strumenti di Novell possono essere installati ed eseguiti in Windows e macOS. Sono consigliate le configurazioni seguenti:

- Windows 10 con Visual Studio 2019
- macOS Mojave (10.11 +) con Visual Studio 2019 per Mac

Novell fornisce un programma di installazione unificato che consente di configurare il sistema con gli strumenti Java, Android e Novell prerequisiti (inclusa una finestra di progettazione visiva per i layout dello schermo). Per istruzioni dettagliate, vedere la [Guida all'installazione di Novell. Android](~/android/get-started/installation/index.md) .

È possibile compilare e testare le applicazioni in un dispositivo reale senza alcuna licenza da Google. Tuttavia, per distribuire l'applicazione tramite uno Store, ad esempio Google Play, Amazon &amp; o Barnes Noble, è possibile che venga addebitata una tariffa di registrazione all'operatore. Google Play pubblicherà l'app immediatamente, mentre gli altri archivi avranno un processo di approvazione simile a quello di Apple.

### <a name="windows"></a>Windows

Le app di Windows (WinForms, WPF o UWP) sono compilate con Visual Studio. Non usano direttamente Novell. Tuttavia, C# il codice può essere condiviso tra Windows, iOS e Android.
Visitare il [centro per sviluppatori](https://developer.microsoft.com/) di Microsoft per informazioni sugli strumenti necessari per lo sviluppo in Windows.

## <a name="creating-the-user-interface-ui"></a>Creazione dell'interfaccia utente

Un vantaggio principale dell'uso di Novell è che l'interfaccia utente dell'applicazione usa controlli nativi in ogni piattaforma, creando app che non sono distinguibili da un'applicazione scritta in Objective-C o Java (per iOS e Android rispettivamente).

Quando si compilano schermate nell'app, è possibile disporre i controlli nel codice o creare schermate complete usando gli strumenti di progettazione disponibili per ogni piattaforma.

### <a name="create-controls-programmatically"></a>Creare controlli a livello di codice

Ogni piattaforma consente di aggiungere controlli dell'interfaccia utente a una schermata usando il codice. Questa operazione può richiedere molto tempo, poiché può essere difficile visualizzare la progettazione terminata quando le coordinate dei pixel di codifica hardcoded per le posizioni e le dimensioni del controllo.

La creazione di controlli a livello di codice presenta tuttavia dei vantaggi, in particolare in iOS per la compilazione di visualizzazioni che ridimensionano o eseguono il rendering in modo diverso nelle dimensioni dello schermo iPhone e iPad.

### <a name="visual-designer"></a>Finestra di progettazione visiva

Ogni piattaforma dispone di un metodo diverso per il layout visivo delle schermate:

- **iOS** : la finestra di progettazione iOS di Novell semplifica la creazione di visualizzazioni usando la funzionalità di trascinamento della selezione e i campi delle proprietà. Complessivamente, queste visualizzazioni costituiscono uno storyboard ed è possibile accedervi nell' **. File storyboard** incluso nel progetto.
- **Android** : Novell fornisce una finestra di progettazione dell'interfaccia utente per il trascinamento della selezione di Android per Visual Studio. I layout dello schermo Android vengono salvati come **. File AXML** quando si usano gli strumenti di Novell.
- **Windows** : Microsoft offre una finestra di progettazione dell'interfaccia utente con trascinamento della selezione in Visual Studio e Blend. I layout della schermata vengono archiviati come. File XAML.

Queste schermate mostrano le finestre di progettazione della schermata visiva disponibili in ogni piattaforma:

 [![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "Queste schermate mostrano le finestre di progettazione della schermata visiva disponibili in ogni piattaforma")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

In tutti i casi, è possibile fare riferimento agli elementi creati visivamente nel codice.

### <a name="user-interface-considerations"></a>Considerazioni sull'interfaccia utente

Un vantaggio fondamentale dell'uso di Novell per la creazione di applicazioni multipiattaforma è la possibilità di sfruttare i vantaggi dei Toolkit di interfaccia utente nativi per presentare un'interfaccia familiare all'utente. L'interfaccia utente eseguirà anche la stessa velocità di qualsiasi altra applicazione nativa.

Alcune metafore dell'interfaccia utente funzionano su più piattaforme (ad esempio, tutte e tre le piattaforme usano un controllo elenco di scorrimento simile), ma per consentire all'applicazione di "sentire" l'interfaccia utente dovrebbe sfruttare gli elementi dell'interfaccia utente specifici della piattaforma quando appropriato. Esempi di metafore dell'interfaccia utente specifiche della piattaforma includono:

- **iOS** : navigazione gerarchica con il pulsante indietro, le schede nella parte inferiore della schermata.
- **Android** : hardware/sistema-pulsante indietro del software, menu azione, schede nella parte superiore della schermata.
- **Windows** : le app di Windows possono essere eseguite su desktop, Tablet (ad esempio, Microsoft Surface) e telefoni. I dispositivi Windows 10 possono includere, ad esempio, il pulsante Indietro hardware e i riquadri animati.

Si consiglia di leggere le linee guida di progettazione pertinenti per le piattaforme di destinazione:

- **iOS** - [linee guida dell'interfaccia umana di Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** - [linee guida dell'interfaccia utente di Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** : [linee guida per la progettazione dell'esperienza utente per Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-re-use"></a>Libreria e riutilizzo del codice

La piattaforma Novell consente di riutilizzare il codice C# esistente in tutte le piattaforme, nonché l'integrazione di librerie scritte in modo nativo per ogni piattaforma.

### <a name="c-source-and-libraries"></a>C#Origine e librerie

Poiché i prodotti Novell C# usano e .NET Framework, un numero elevato di codice sorgente esistente (progetti open source e interni) può essere riutilizzato nei progetti Novell. iOS o Novell. Android. Spesso l'origine può essere semplicemente aggiunta a una soluzione Novell, che funzionerà immediatamente. Se è stata usata una funzionalità di .NET Framework non supportata, potrebbe essere necessario apportare alcune modifiche.

Esempi di C# origine che possono essere usati in Novell. iOS o Novell. Android includono: SQLite-NET, NewtonSoft. JSON e SharpZipLib.

### <a name="objective-c-bindings--binding-projects"></a>Binding di Objective-C + progetti di associazione

Novell fornisce uno strumento denominato *btouch* che consente di creare binding che consentono l'uso di librerie Objective-C nei progetti Novell. iOS. Per informazioni dettagliate su come eseguire questa operazione, vedere la [documentazione relativa ai tipi di binding Objective-C](~/cross-platform/macios/binding/binding-types-reference.md) .

Esempi di librerie Objective-C che è possibile usare in Novell. iOS includono: RedLaser analisi del codice a barre, Google Analytics e integrazione PayPal. Le associazioni Novell. iOS Open Source sono disponibili su [GitHub](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>Associazioni. jar + progetti di associazione

Novell supporta l'uso di librerie Java esistenti in Novell. Android. Per informazioni dettagliate su come usare un, vedere la [documentazione relativa al binding di una libreria Java](~/android/platform/binding-java-library/index.md) . File JAR da Novell. Android.

Le associazioni Novell. Android Open Source sono disponibili su [GitHub](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C tramite PInvoke

La tecnologia "Platform Invoke" (P/Invoke) consente al codiceC#gestito () di chiamare i metodi nelle librerie native, oltre al supporto per le librerie native che richiamano il codice gestito.

Ad esempio, la libreria [sqlite-net](https://github.com/praeclarum/sqlite-net) usa istruzioni analoghe alle seguenti:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Questa operazione viene associata all'implementazione di SQLite in linguaggio C nativo in iOS e Android.
Gli sviluppatori che hanno familiarità con un'API C esistente possono C# costruire un set di classi per eseguire il mapping all'API nativa e usare il codice di piattaforma esistente. È disponibile la documentazione per il [collegamento delle librerie native](~/ios/platform/native-interop.md) in Novell. iOS. i principi simili si applicano a Novell. Android.

### <a name="c-via-cppsharp"></a>C++tramite CppSharp

Miguel spiega CXXI (ora denominato [CppSharp](https://github.com/mono/CppSharp)) sul suo [Blog](https://tirania.org/blog/archive/2011/Dec-19.html). Un'alternativa all'associazione diretta a C++ una libreria consiste nel creare un wrapper C e associarlo a tale tramite P/Invoke.
