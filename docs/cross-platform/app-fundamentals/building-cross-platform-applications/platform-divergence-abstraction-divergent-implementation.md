---
title: "Parte 4 - gestione di più piattaforme"
ms.topic: article
ms.prod: xamarin
ms.assetid: BBE47BA8-78BC-6A2B-63BA-D1A45CB1D3A5
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 21cd08ad2eb9c78ba1bcd6b31400a38266c65e51
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="part-4---dealing-with-multiple-platforms"></a>Parte 4 - gestione di più piattaforme

## <a name="handling-platform-divergence-amp-features"></a>La gestione della piattaforma divergenza &amp; funzionalità

Una divergenza non è semplicemente un problema 'multipiattaforma'; i dispositivi nella piattaforma 'stesso' hanno funzionalità diverse (in particolare l'ampia gamma di dispositivi Android sono disponibili). Il modo più ovvio e base è dimensioni dello schermo, ma altri attributi del dispositivo possono variare e richiedere un'applicazione di controllare per alcune funzionalità e si comportano in modo diverso in base alla loro presenza (o assenza).

Ciò significa che tutte le applicazioni devono affrontare una riduzione del funzionamento della funzionalità oppure presentare un insieme di funzionalità minimo comune denominatore indesiderati. Integrazione di Xamarin con il SDK di nativo di ogni piattaforma consentono alle applicazioni di sfruttare funzionalità specifiche della piattaforma, pertanto è opportuno progettare app di usare tali funzionalità.

Vedere la documentazione di funzionalità della piattaforma per una panoramica delle differenziano tra le piattaforme di funzionalità.

 <a name="Examples_of_Platform_Divergence" />


### <a name="examples-of-platform-divergence"></a>Esempi di divergenza di piattaforma

 <a name="Fundamental_elements_that_exist_across_platforms" />


#### <a name="fundamental-elements-that-exist-across-platforms"></a>Elementi fondamentali che esistono tra le piattaforme

Esistono alcune caratteristiche di applicazioni per dispositivi mobili che sono universale.
Questi sono i concetti di livello superiore che in genere valgono per tutti i dispositivi e possono pertanto costituiscono la base della progettazione dell'applicazione:

-  Selezione funzionalità tramite le schede o i menu
-  Elenchi di dati e lo scorrimento
-  Singole visualizzazioni dei dati
-  Modifica sola visualizzazioni di dati
-  Spostamento indietro


Quando si progetta il flusso di alto livello schermata è possibile basare un'esperienza utente più comuni su questi concetti.

 <a name="platform-specific_attributes" />


#### <a name="platform-specific-attributes"></a>attributi specifici della piattaforma

Oltre agli elementi di base presenti in tutte le piattaforme, è necessario adattarlo alle variazioni piattaforma chiave nella progettazione. Potrebbe essere necessario prendere in considerazione queste differenze (e scrivere codice in modo specifico per la gestione):

-   **Schermata di dimensioni** – alcune piattaforme (ad esempio, iOS e le versioni precedenti di Windows Phone) usa le dimensioni dello schermo che sono relativamente semplici di destinazione. I dispositivi Android hanno un'ampia gamma di dimensioni dello schermo, che richiedono ulteriori operazioni per il supporto dell'applicazione.
-   **Metafore navigazione** – differiscono tra le piattaforme (ad es. pulsante "Indietro" hardware, controllo dell'interfaccia utente Panorama) e all'interno di piattaforme (Android 2 e 4, iPhone e iPad).
-   **Tastiere** : i dispositivi Android alcune hanno tastiere fisiche mentre altri hanno solo una tastiera software. Codice che rileva se una tastiera di soft copre parte della schermata deve essere riservati per queste differenze.
-   **Tocco e i movimenti** – varia a supporto del sistema operativo per il riconoscimento di movimento, soprattutto nelle versioni precedenti di ogni sistema operativo. Le versioni precedenti di Android sono molto un supporto limitato per le operazioni di tocco, vale a dire che supporta dispositivi precedenti potrebbe richiedere codice separato
-   **Le notifiche push** : sono presenti funzionalità o implementazioni diverse a seconda della piattaforma (ad es. In Windows, in tempo reale riquadri).


 <a name="Device-specific_features" />


#### <a name="device-specific-features"></a>Funzionalità specifiche di dispositivo

Determinare quali le funzionalità minime necessarie per l'applicazione devono essere; o quando decide quali funzionalità aggiuntive per sfruttare in ciascuna piattaforma. Codice necessario per rilevare le funzionalità e disabilitare la funzionalità o offrono alternative (ad es. un'alternativa alla località geografica è possibile consentire all'utente di digitare un percorso o scegliere da una mappa):

-   **Fotocamera** – funzionalità varia tra i dispositivi: alcuni dispositivi non dispongono di una fotocamera, gli altri sono entrambe le telecamere anteriore e retro. Alcune fotocamere sono in grado di registrare video.
-   **Posizione geografica e mappe** : supporto per la posizione GPS o Wi-Fi non è presente in tutti i dispositivi. Le applicazioni è inoltre necessario tenere in considerazione per i diversi livelli di precisione supportato da ogni metodo.
-   **Accelerometro, Giroscopio e compasso** – queste funzionalità sono spesso disponibili in solo una selezione di dispositivi in ogni piattaforma, in modo App quasi sempre necessario fornire un fallback quando l'hardware non è supportato.
-   **Twitter e Facebook** : solo 'predefinito' iOS5 e iOS6 rispettivamente. In versioni precedenti e altre piattaforme è necessario fornire le funzioni di autenticazione e interagiscono direttamente con ogni API dei servizi.
-   **In prossimità di campo Communications (NFC)** : solo in (alcune) telefoni Android (in fase di scrittura).


 <a name="Dealing_with_Platform_Divergence" />


### <a name="dealing-with-platform-divergence"></a>Gestione di divergenza di piattaforma

Esistono due approcci diversi per il supporto di più piattaforme dalla stessa codebase, ognuno con un proprio set di vantaggi e svantaggi.

-   **Piattaforma astrazione** – modello di facciata Business, fornisce un accesso unificato tra le piattaforme e astrae le implementazioni di piattaforma particolare in un'API unificata.
-   **Implementazione divergenti** : chiamata di piattaforma specifica funzionalità tramite le implementazioni divergenti tramite gli strumenti di architettura, ad esempio l'ereditarietà e interfacce o di compilazione condizionale.


 <a name="Platform_Abstraction" />


## <a name="platform-abstraction"></a>Astrazione della piattaforma

 <a name="Class_Abstraction" />


### <a name="class-abstraction"></a>Astrazione di classe

Tramite le interfacce o classi di base definito nel codice condiviso e implementato o estesi in progetti specifici della piattaforma. Scrittura e l'estensione di codice condiviso con astrazioni di classe è particolarmente adatto per librerie di classi portabili perché sono un sottoinsieme limitato di framework disponibili e non può contenere le direttive del compilatore per supportare i rami del codice specifico della piattaforma.

 <a name="Interfaces" />


#### <a name="interfaces"></a>Interfacce

Utilizzo di interfacce consente di implementare le classi specifiche della piattaforma che possono ancora essere passate nelle librerie condivise di sfruttare codice comune.

L'interfaccia è definito nel codice condiviso e la libreria condivisa passato come parametro o proprietà.

Le applicazioni specifiche della piattaforma possono quindi implementare l'interfaccia e comunque sfruttare il codice condiviso ' elaborarla '.

 **Vantaggi**

L'implementazione può contenere codice specifico della piattaforma e anche fare riferimento a librerie esterne specifico della piattaforma.

 **Svantaggi**

La necessità di creare e passare le implementazioni il codice condiviso. Se l'interfaccia viene utilizzata nel codice condiviso quindi ma finisce per essere passati più parametri di metodo o in caso contrario propagato attraverso la catena di chiamate. Se il codice condiviso che utilizzano grandi quantità di diverse interfacce quindi sono devono tutti essere creati e impostati nel codice condiviso in un punto.

 <a name="Inheritance" />


#### <a name="inheritance"></a>Ereditarietà

Il codice condiviso è possibile implementare le classi astratte o virtuale che può essere estesa in uno o più progetti specifici della piattaforma. Questa operazione è simile all'utilizzo di interfacce, ma con un comportamento già implementata. Esistono diversi punti di vista che interfacce o ereditarietà siano una scelta migliore di progettazione: poiché c# consente solo l'ereditarietà singola in particolare può determinano il modo in cui è possono progettare le API in futuro. Utilizzare l'ereditarietà con cautela.

I vantaggi e svantaggi delle interfacce si applicano ugualmente a ereditarietà, con l'ulteriore vantaggio di classe di base può contenere un codice di implementazione (ad esempio un'intera piattaforma agnostico implementazione che può essere estese facoltativamente).

<a name="Xamarin.Forms" />

### <a name="xamarinforms"></a>Xamarin.Forms

Vedere il [xamarin. Forms](~/xamarin-forms/get-started/index.md) documentazione.


### <a name="plug-in-cross-platform-functionality"></a>Funzionalità di plug-in multipiattaforma

È anche possibile estendere le app multipiattaforma in modo coerente utilizzando i plug-in.

Collegato dal nostro [plug-in github](https://github.com/xamarin/plugins), la maggior parte dei plug-in sono open source progetti (in genere disponibili per l'installazione tramite Nuget) che consentono di implementano un'ampia gamma di funzionalità specifiche della piattaforma di stato della batteria per le impostazioni con un API comuni che è facile da usare in App della piattaforma Xamarin e xamarin. Forms.


<a name="Other_Cross-Platform_Libraries" />

### <a name="other-cross-platform-libraries"></a>Altre librerie multipiattaforma

Esistono alcune librerie di terze parti 3rd disponibili che forniscono funzionalità multipiattaforma di:

-   **MvvmCross** -  [https://github.com/slodge/MvvmCross/](https://github.com/slodge/MvvmCross/)
-   **Comunemente** (per la localizzazione -)  [https://github.com/rdio/vernacular/](https://github.com/rdio/vernacular/)
-   **MonoGame** (per i giochi XNA) -  [http://monogame.codeplex.com/](http://monogame.codeplex.com/)
-   **NGraphics** - [NGraphics](https://github.com/praeclarum/NGraphics) e il relativo precursore [https://github.com/praeclarum/CrossGraphics](https://github.com/praeclarum/CrossGraphics)


 <a name="Divergent_Implementation" />


### <a name="divergent-implementation"></a>Implementazione divergenti

 <a name="Conditional_Compilation" />


#### <a name="conditional-compilation"></a>Compilazione condizionale

Esistono alcune situazioni in cui il codice condiviso dovranno comunque a funzionare in modo diverso in ogni piattaforma, probabilmente l'accesso a classi o funzionalità che si comportano in modo diverso. Compilazione condizionale funziona meglio con i progetti Asset condivisi, in cui lo stesso file di origine viene fatto riferimento in più progetti che sono disponibili diversi simboli definiti.

Progetti di Xamarin è sempre necessario definiscono `__MOBILE__` vale per iOS e i progetti di applicazione Android (si noti il doppio carattere di sottolineatura pre- e post-correggere questi simboli).

```csharp
#if __MOBILE__
// Xamarin iOS or Android-specific code
#endif
```

<a name="iOS" />

##### <a name="ios"></a>iOS

Xamarin definisce `__IOS__` che è possibile utilizzare per rilevare i dispositivi iOS.

```csharp
#if __IOS__
// iOS-specific code
#endif
```

Sono disponibili anche i simboli specifici di TV ed espressioni di controllo:

```csharp
#if __TVOS__
// tv-specific stuff
#endif

#if __WATCHOS__
// watch-specific stuff
#endif
```

<a name="Android" />

##### <a name="android"></a>Android

Il codice che deve essere compilato solo in applicazioni di xamarin possa usare le operazioni seguenti

```csharp
#if __ANDROID__
// Android-specific code
#endif
```

Ogni versione dell'API definisce anche una direttiva del compilatore nuova, in modo consentirà di codice simile al seguente si aggiungono funzionalità se sono destinate le API più recente. Ogni livello dell'API include tutti i simboli 'inferiori' livello. Questa funzionalità non è molto utile per il supporto di più piattaforme; in genere il `__ANDROID__` simbolo saranno sufficiente.

```csharp
#if __ANDROID_11__
// code that should only run on Android 3.0 Honeycomb or newer
#endif
```

##### <a name="mac"></a>Mac

Non è attualmente un simbolo predefinito per Xamarin.Mac, ma è possibile aggiungere un progetto di app nel Mac **Opzioni > compilare > compilatore** nel **definire simboli** casella o modificare il **csproj**  file e aggiungere sono (ad esempio `__MAC__`)

```xml
<PropertyGroup><DefineConstants>__MAC__;$(DefineConstants)</DefineConstants></PropertyGroup>
```

<a name="Windows_Phone" />

##### <a name="windows-phone"></a>Windows Phone

App di Windows Phone definisce due simboli: `WINDOWS_PHONE` e `SILVERLIGHT` – che può essere utilizzato per il codice per la piattaforma di destinazione. Questi non sono i caratteri di sottolineatura circostante come i simboli di piattaforma Xamarin eseguire.


<a name="Using_Conditional_Compilation" />

##### <a name="using-conditional-compilation"></a>Utilizzo della compilazione condizionale

Il percorso del file per il file di database SQLite nell'impostazione di un semplice esempio di case study di compilazione condizionale. Tre piattaforme hanno requisiti leggermente diversi per specificare il percorso del file:

-   **iOS** : Apple preferito non utente dati da inserire in un percorso specifico (la directory di libreria), ma non esiste alcun costante di sistema per questa directory. Per compilare il percorso corretto, è necessario codice specifico della piattaforma.
-   **Android** : il percorso di sistema restituito da `Environment.SpecialFolder.Personal` è un percorso per archiviare il file di database accettabile.
-   **Windows Phone** : il meccanismo di memorizzazione isolato non consente un percorso completo di specificare solo un nome e percorso relativo.
-   **Piattaforma Windows universale** – utilizza `Windows.Storage` API.

Il codice seguente usa la compilazione condizionale per garantire il `DatabaseFilePath` sia corretto per ogni piattaforma:

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

Il risultato è una classe che può essere compilata e usata in tutte le piattaforme, inserire il file di database SQLite in un percorso diverso in ogni piattaforma.

