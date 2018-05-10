---
title: 'Parte 1: informazioni sulla piattaforma Xamarin Mobile'
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 10d1b0786d5ca0b42b6e6c3e2f2a08edb7a37591
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1: informazioni sulla piattaforma Xamarin Mobile

La piattaforma di Xamarin è costituita da un numero di elementi che consentono di sviluppare applicazioni per iOS e Android:

-   **Linguaggio c#** : consente di utilizzare una sintassi familiare e caratteristiche avanzate quali i Generics, LINQ e Task Parallel Library.
-   **Mono .NET framework** – fornisce un'implementazione di più piattaforme di funzionalità complete di Microsoft .NET Framework.
-   **Compilatore** : a seconda della piattaforma, produce un'app nativa (ad es. iOS) o un'applicazione .NET integrata e runtime (ad es. Android). Il compilatore esegue inoltre molte ottimizzazioni per la distribuzione per dispositivi mobili, ad esempio il collegamento di stoccaggio codice non usato.
-   **Strumenti IDE** : Visual Studio su Mac e Windows consente di creare, compilare e distribuire progetti di Xamarin.


Inoltre, poiché la lingua sottostante è c# con .NET framework, i progetti possono essere strutturati per condividere il codice che può essere distribuito anche per Windows Phone.

 <a name="Under_the_Hood" />


## <a name="under-the-hood"></a>Dietro le quinte

Sebbene Xamarin consente di scrivere applicazioni in c# e condividere lo stesso codice su più piattaforme, l'implementazione effettiva in ogni sistema è molto diversa.

 <a name="Compilation" />


## <a name="compilation"></a>Compilazione

Il codice sorgente c# procede in un'applicazione nativa in modi molto diversi in ogni piattaforma:

-   **iOS** – c# è ahead di tempo (AOT) compilata in linguaggio assembly ARM. .NET framework è incluso, con classi inutilizzate viene rimosse durante il collegamento per ridurre le dimensioni dell'applicazione. Apple non consente la generazione del codice di runtime in iOS, alcune funzionalità del linguaggio non disponibili (vedere [xamarin limitazioni](~/ios/internals/limitations.md) ).
-   **Android** – c# viene compilata a livello di integrità e incluso nel pacchetto MonoVM + JIT'ing. Classi inutilizzate in framework vengono rimossi durante il collegamento. L'applicazione viene eseguito side-by-side con Java/ART (runtime di Android) e interagisce con i tipi nativi tramite JNI (vedere [xamarin limitazioni](~/android/internals/limitations.md) ).
-   **Windows** – c# viene compilato in linguaggio intermedio eseguito dal runtime predefinito e non richiede gli strumenti di Xamarin. Progettazione di applicazioni di Windows linee guida seguenti di Xamarin, è più semplice riutilizzare il codice in iOS e Android.
  Si noti che la piattaforma Windows universale ha anche un **.NET Native** opzione che si comporta in modo analogo alla compilazione AOT di xamarin.


La documentazione del linker [xamarin](~/ios/deploy-test/linker.md) e [xamarin](~/android/deploy-test/linker.md) fornisce ulteriori informazioni su questa parte del processo di compilazione.

Runtime 'compilation': la generazione di codice in modo dinamico con `System.Reflection.Emit` : deve essere evitato.

Il kernel di Apple impedisce la generazione del codice dinamico nei dispositivi iOS, pertanto la creazione di codice il volo non funzionano in xamarin. IOS. Analogamente, le funzionalità di Dynamic Language Runtime non possono essere utilizzate con strumenti Xamarin.

Alcune funzionalità di reflection di lavoro (ad es. MonoTouch.Dialog utilizza per l'API di Reflection), ma non la generazione di codice.

 <a name="Platform_SDK_Access" />


## <a name="platform-sdk-access"></a>Accesso SDK della piattaforma

Xamarin esegue le funzionalità fornite dal SDK specifici della piattaforma facilmente accessibili con sintassi familiare c#:

-   **iOS** – xamarin espone Framework SDK CocoaTouch Apple come spazi dei nomi che è possibile fare riferimento da c#. Ad esempio il framework UIKit che contiene tutti i controlli dell'interfaccia utente può essere incluso con un semplice `using UIKit;` istruzione.
-   **Android** – xamarin espone Google Android SDK come spazi dei nomi, pertanto è possibile fare riferimento a qualsiasi parte del SDK supportato con un'istruzione, ad esempio `using Android.Views;` per i controlli dell'interfaccia utente di accedere.
-   **Windows** : app di Windows vengono compilate utilizzando Visual Studio in Windows. Tipi di progetto includono Windows Form, WPF, WinRT e la piattaforma UWP (Universal Windows).


 <a name="Seamless_Integration_for_Developers" />


## <a name="seamless-integration-for-developers"></a>Integrazione perfetta per gli sviluppatori

Il vantaggio di Xamarin è che nonostante le differenze dietro le quinte, xamarin. IOS e xamarin (associato a Windows SDK Microsoft) offrono un'esperienza trasparente per la scrittura di codice c# che può essere riutilizzato in tutte e tre le piattaforme.

Logica di business, l'utilizzo del database, accesso alla rete e altre funzioni comuni di essere scritti una sola volta e riutilizzate in ogni piattaforma, fornire una base per le interfacce utente specifiche della piattaforma che osservare ed eseguono come applicazioni native.

 <a name="Integrated_Development_Environment_(IDE)_Availability" />


## <a name="integrated-development-environment-ide-availability"></a>Disponibilità di sviluppo integrato (IDE) di ambiente

In Visual Studio in Windows o Mac, è possibile eseguire lo sviluppo con Xamarin. L'IDE scelto verrà determinata dalle piattaforme di destinazione desiderato.

Poiché le app di Windows possono essere sviluppate solo in Windows, di compilazione per iOS, Android, _e_ Windows richiede Visual Studio per Windows. Tuttavia è possibile condividere file tra i computer Windows e Mac, in modo App iOS e Android può essere compilata in un Mac e il codice condiviso in un secondo momento può essere aggiunto a un progetto di Windows e progetti.

I requisiti di sviluppo per ogni piattaforma vengono descritti più dettagliatamente il [requisito](~/cross-platform/get-started/requirements.md) Guida.


<a name="iOS" />

### <a name="ios"></a>iOS

Lo sviluppo di applicazioni iOS richiede un computer Mac, macOS. È inoltre possibile utilizzare Visual Studio per scrivere e distribuire applicazioni iOS con Xamarin in Visual Studio. Tuttavia, un computer Mac è ancora necessario per la compilazione e scopi di gestione delle licenze.

Apple Xcode IDE deve essere installato per fornire il compilatore e il simulatore per il test. È possibile testare nei propri dispositivi [gratuitamente](~/ios/get-started/installation/device-provisioning/free-provisioning.md), ma per compilare applicazioni per la distribuzione (ad es. App Store) è necessario aggiungere il programma per sviluppatori di Apple (99 dollari all'anno). Ogni volta che si invia o si aggiorna un'applicazione, deve essere rivisto e approvato da Apple prima di renderlo disponibile per il download.

Codice è scritto con l'IDE di Visual Studio e layout dello schermo può essere compilato a livello di codice o modificati con iOS della Xamarin finestra di progettazione nell'IDE.

Consultare la [Guida all'installazione di xamarin](~/ios/get-started/installation/index.md) per istruzioni dettagliate sulla configurazione.

<a name="Android" />

### <a name="android"></a>Android

Sviluppo di applicazioni Android richiede Java e Android SDK sia installato. Che forniscono il compilatore, l'emulatore e altri strumenti richiesti per la compilazione, distribuzione e test. Gli strumenti Java, Google Android SDK e di Xamarin possono tutti essere installati ed eseguiti le seguenti configurazioni:

-  Mac OS X montagna di El Capitan e versioni successive (10.11 +) con Visual Studio per Mac
-  Windows 7 e versioni successive con Visual Studio 2015 o 2017


Xamarin offre un programma di installazione unificata che configurerà il sistema con Java prerequisito, strumenti di Android e Xamarin (incluso un finestra di progettazione visiva per il layout dello schermo). Consultare la [Guida all'installazione di xamarin](~/android/get-started/installation/index.md) per istruzioni dettagliate.

È possibile compilare e testare applicazioni in un dispositivo effettivo senza alcuna licenza da Google, tuttavia per distribuire l'applicazione tramite un archivio (ad esempio Google Play, Amazon o Barnes &amp; Noble) una tariffa di registrazione può essere dovuta all'operatore. Google Play pubblicherà l'applicazione immediatamente, mentre altri archivi di dispongono di un processo di approvazione simile in Apple.

 <a name="Windows_Phone" />


### <a name="windows"></a>WINDOWS

App di Windows (Windows Form, WPF o UWP) vengono compilate con Visual Studio. Non utilizzare Xamarin direttamente. Tuttavia, il codice c# può essere condivise tra Windows, iOS e Android.
Visitare Microsoft [Dev Center](https://developer.microsoft.com/) per apprendere gli strumenti necessari per lo sviluppo di Windows.

 <a name="Creating_the_User_Interface_(UI)" />


## <a name="creating-the-user-interface-ui"></a>Creazione dell'interfaccia utente

Un vantaggio chiave con Xamarin è che l'interfaccia utente dell'applicazione utilizza controlli nativi in ciascuna piattaforma, creazione di App che non sono distinguibili da un'applicazione scritta in Objective-C o Java (per iOS e Android rispettivamente).

Quando si compila le schermate dell'App, è possibile disporre i controlli nel codice o creare schermate completate utilizzando gli strumenti di progettazione disponibili per ogni piattaforma.

 <a name="Programmatically_Create_Controls" />


### <a name="create-controls-programmatically"></a>Creare i controlli a livello di codice

Ogni piattaforma consente a controlli dell'interfaccia da aggiungere a una schermata tramite codice utente. Può essere molto tempo poiché può essere difficile visualizzare il progetto finito quando pixel a livello di codice le coordinate per dimensioni e posizioni del controllo.

Creazione di controlli a livello di programmazione dispone vantaggi, tuttavia, in particolare in iOS per la creazione di viste che ridimensionare o eseguire il rendering in modo diverso tra le dimensioni dello schermo di iPhone e iPad.

 <a name="Visual_Designer" />


### <a name="visual-designer"></a>Finestra di progettazione visiva

Ogni piattaforma presenta un metodo diverso per il layout visivo degli schermi:

-   **iOS** – iOS di Xamarin Designer facilita la creazione di viste utilizzando i campi di proprietà e funzionalità di trascinamento e rilascio. Queste viste collettivamente costituiscono uno Storyboard e sono accessibili nel **. Storyboard** file incluso nel progetto.
-   **Android** – Xamarin offre una progettazione dell'interfaccia utente di trascinamento e rilascio Android di Visual Studio. Layout dello schermo Android vengono salvati come **. AXML** file quando si utilizzano strumenti di Xamarin.
-   **Windows** – Microsoft fornisce una finestra di progettazione dell'interfaccia utente trascinamento e rilascio in Visual Studio e Blend. Il layout dello schermo vengono archiviati come. File XAML.


Queste schermate mostrano le finestre di progettazione schermata visual disponibile in ogni piattaforma:

 [ ![](understanding-the-xamarin-mobile-platform-images/designer-all1.png "Queste schermate mostrano le finestre di progettazione schermata visual disponibili in ciascuna piattaforma")](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

In tutti i casi, gli elementi creati visivamente possono fare riferimento nel codice.

 <a name="User_Interface_Considerations" />


### <a name="user-interface-considerations"></a>Considerazioni sull'interfaccia utente

Un vantaggio chiave con Xamarin per compilare applicazioni multipiattaforma è che è possibile sfruttare Toolkit dell'interfaccia utente nativo per presentare un'interfaccia familiare all'utente. L'interfaccia utente verrà inoltre risulteranno meno veloci come qualsiasi altra applicazione nativa.

Alcuni metafore dell'interfaccia utente di lavoro su più piattaforme (ad esempio, tutti e tre le piattaforme utilizzano un controllo elenco scorrimento simile) ma affinché l'applicazione ' sentirsi ' destra dell'interfaccia utente deve sfruttare utente specifico della piattaforma elementi dell'interfaccia quando appropriato. Esempi di metafore dell'interfaccia utente specifiche della piattaforma:

-   **iOS** : navigazione gerarchica con pulsante Indietro soft schede nella parte inferiore della schermata.
-   **Android** hardware o software del sistema, eseguire il pulsante, dal menu azione, le schede nella parte superiore di schermata.
-   **Windows** : app di Windows possono essere eseguite sul desktop, Tablet (ad esempio Microsoft Surface) e telefoni. I dispositivi Windows 10 possono disporre di hardware pulsante Indietro e riquadri in tempo reale, ad esempio.


È consigliabile leggere le linee guida di progettazione rilevanti per le piattaforme di destinazione:

-   **iOS** – [linee guida dell'interfaccia umana Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
-   **Android** – [linee guida dell'interfaccia utente di Google](http://developer.android.com/guide/practices/ui_guidelines/index.html)
-   **Windows** – [indicazioni per la progettazione di esperienza utente per Windows](https://developer.microsoft.com/windows/design)


 <a name="Library_and_Code_Re-use" />


## <a name="library-and-code-re-use"></a>Raccolta e il riutilizzo del codice

Sulla piattaforma Xamarin consente il riutilizzo del codice c# esistente tra tutte le piattaforme, nonché l'integrazione di librerie scritte in modo nativo per ogni piattaforma.

 <a name="C#_Source_and_Libraries" />


### <a name="c-source-and-libraries"></a>Librerie e codice sorgente c#

Perché Xamarin prodotti usano c# e .NET framework, un numero elevato di codice sorgente esistente (entrambi aprire progetti interni e origine) può essere riutilizzato in progetti di xamarin. IOS o xamarin. Spesso l'origine può semplicemente aggiunto a una soluzione Xamarin e verrà applicata immediatamente. Se è stata utilizzata una funzionalità di .NET framework non supportata, è possibile che alcune modifiche di entità potrebbero essere necessari.

Esempi di codice sorgente c# che può essere utilizzato in xamarin. IOS o xamarin: SQLite NET, newtonsoft. JSON e SharpZipLib.

 <a name="Objective-C_Bindings_+_Binding_Projects" />


### <a name="objective-c-bindings--binding-projects"></a>Associazioni Objective-C + progetti di associazione

Xamarin offre uno strumento denominato *btouch* che consente di creare associazioni che consentono a librerie Objective-C essere utilizzata nei progetti di xamarin. IOS. Consultare il [documentazione sui tipi di associazione di Objective-C](~/cross-platform/macios/binding/binding-types-reference.md) per informazioni dettagliate su questa procedura.

Esempi di librerie Objective-C che possono essere usate in xamarin. ios: codice a barre RedLaser l'analisi, l'integrazione Analitica Google e PayPal. Binding xamarin open source disponibili in [github](https://github.com/mono/monotouch-bindings).

 <a name=".jar_Bindings_+_Binding_Projects" />


### <a name="jar-bindings--binding-projects"></a>associazioni JAR + progetti di associazione

Xamarin supporta l'utilizzo delle librerie di Java esistente in xamarin. Fare riferimento al [una documentazione Java libreria associazione](~/android/platform/binding-java-library/index.md) per informazioni dettagliate su come usare una. File JAR di xamarin.

Binding xamarin open source disponibili in [github](https://github.com/mono/monodroid-bindings).

 <a name="C_via_PInvoke" />


### <a name="c-via-pinvoke"></a>C tramite PInvoke

Tecnologia "Platform Invoke" (P/Invoke) consente al codice gestito (c#) per chiamare i metodi nelle librerie native, nonché il supporto per librerie native chiamare nuovamente nel codice gestito.

Ad esempio, il [SQLite NET](https://github.com/praeclarum/sqlite-net) libreria utilizza istruzioni simile al seguente:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

In questo viene associato all'implementazione del linguaggio C SQLite nativa in iOS e Android.
Gli sviluppatori esperti in un'API C esistente è possono costruire un set di classi c# per eseguire il mapping all'API native e utilizzare il codice di piattaforma esistente. È la documentazione relativa a [collegamento librerie native](~/ios/platform/native-interop.md) in xamarin. IOS, simile principi per xamarin.

 <a name="C++_via_Cxxi" />


### <a name="c-via-cppsharp"></a>C++ tramite CppSharp

Miguel spiega CXXI (ora chiamato [CppSharp](https://github.com/mono/CppSharp)) su his [blog](http://tirania.org/blog/archive/2011/Dec-19.html). Un'alternativa all'associazione direttamente a una libreria di C++ consiste nel creare un wrapper di C e associare a quella tramite P/Invoke.

