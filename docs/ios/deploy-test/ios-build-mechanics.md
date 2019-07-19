---
title: Meccanismi di compilazione per iOS
description: Questa guida esplora come temporizzare le app e usare metodi che permettono compilazioni più rapide per tutte le configurazioni di compilazione.
ms.prod: xamarin
ms.assetid: 06FD3940-D666-4C9E-BC3E-BBE481EF8012
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: dbf8f85192f9052041fc65367eb2f6c57603b440
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831430"
---
# <a name="ios-build-mechanics"></a>Meccanismi di compilazione per iOS

_Questa guida esplora come pianificare le app e usare metodi che permettono compilazioni più rapide per tutte le configurazioni di compilazione._

Lo sviluppo di applicazioni ottimali comporta molto più della semplice scrittura del codice appropriato. Un'app ben scritta deve contenere ottimizzazioni che permettano compilazioni più rapide con app di dimensioni minori e dall'esecuzione più veloce. Queste ottimizzazioni produrranno un'esperienza migliore non solo per l'utente, ma anche per qualsiasi sviluppatore che lavora al progetto. Nel gestire l'app, è essenziale assicurarsi di temporizzare spesso ogni elemento. 

Ricordare che le opzioni predefinite sono sicure e veloci, ma non sempre ottimali per ogni situazione. Inoltre, molte opzioni possono rallentare o accelerare il ciclo di sviluppo, a seconda di ogni singolo progetto. Ad esempio, la rimozione nativa richiede tempo, ma pur raggiungendo dimensioni molto ridotte, il tempo impiegato per la rimozione non verrà recuperato attraverso una distribuzione più veloce. D'altro canto, la rimozione nativa può ridurre l'app significativamente, caso in cui sarà più rapida da distribuire. Questi aspetti variano tra un progetto e l'altro e l'unico modo per essere sicuri è testare il progetto.

Le velocità di compilazione di Xamarin possono anche dipendere dalle diverse capacità e funzionalità di un computer, che a loro volta possono influire sulle prestazioni: capacità del processore, velocità del bus, quantità di memoria fisica, velocità dei dischi e velocità di rete. Queste limitazioni delle prestazioni non rientrano nell'ambito di questo documento e dovranno essere gestite dallo sviluppatore.


## <a name="timing-apps"></a>Temporizzazione delle app

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per abilitare l'output di MSBuild di diagnostica in Visual Studio per Mac:

1. Fare clic su **Visual Studio per Mac > Preferenze...**
2. Nella visualizzazione albero a sinistra selezionare **Progetti > Compila**
3. Nel pannello a destra impostare l'elenco a discesa del livello di dettaglio dei log su **Diagnostica**:  [![](ios-build-mechanics-images/image2.png "Impostazione del livello di dettaglio del log")](ios-build-mechanics-images/image2.png#lightbox)
4. Fare clic su **OK**
5. Riavviare Visual Studio per Mac
6. Pulire e ricompilare il pacchetto
7. Visualizzare l'output di diagnostica nel riquadro degli errori (Visualizza > Riquadri > Errori) facendo clic sul pulsante Output di compilazione


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per abilitare l'output di MSBuild di diagnostica in Visual Studio:

1. Fare clic su **Strumenti > Opzioni...**
2. Nella visualizzazione albero a sinistra selezionare **Progetti e soluzioni > Compila ed esegui**
3. Nel pannello a destra impostare l'*elenco a discesa del livello di dettaglio dell'output di compilazione di MSBuild* su **Diagnostica**:  [![](ios-build-mechanics-images/image2-vs.png "Impostazione del livello di dettaglio dell'output di compilazione di MSBuild")](ios-build-mechanics-images/image2-vs.png#lightbox)
4. Fare clic su **OK**
5. Pulire e ricompilare il pacchetto.
6. L'output di diagnostica viene visualizzato nel pannello Output.

-----

## <a name="timing-mtouch"></a>Temporizzazione di mtouch

Per visualizzare informazioni specifiche del processo di compilazione di mtouch, passare `--time --time` agli argomenti di mtouch in **Opzioni progetto**. I risultati sono disponibili in Output di compilazione, cercando l'attività `MTouch`:

```csharp
Setup: 36 ms
Resolve References: 982 ms
Extracted native link info: 987 ms
...
Total time: 1554 ms
```

## <a name="connecting-from-visual-studio-with-build-host"></a>Connessione da Visual Studio all'host di compilazione

Gli strumenti di Xamarin funzionano tecnicamente su qualsiasi Mac che esegue OS X 10.10 Yosemite o versioni successive. Tuttavia, le prestazioni del Mac possono influire negativamente sulle esperienze di sviluppo e sui tempi di compilazione.

Nello stato disconnesso Visual Studio in Windows esegue solo la fase di compilazione in C# e non tenta di eseguire il collegamento o la compilazione AOT, di creare un pacchetto dell'app in un bundle con estensione  _app_  o di firmare il bundle dell'app. Raramente la fase di compilazione in C# è un collo di bottiglia per le prestazioni. Provare a individuare il punto nella pipeline in cui la compilazione viene rallentata eseguendo la compilazione direttamente nell'host di compilazione Mac in Visual Studio per Mac.


Inoltre, uno dei motivi di rallentamento più comuni è la connessione di rete tra il computer Windows e l'host di compilazione Mac. Questo problema può essere dovuto a un impedimento fisico nella rete, all'uso di una connessione wireless o alla necessità di eseguire la connessione tramite un computer già troppo carico, ad esempio un servizio Mac nel cloud.

## <a name="simulator-tricks"></a>Suggerimenti sul simulatore

 
Nello sviluppare applicazioni per dispositivi mobili, è essenziale distribuire il codice rapidamente. Per svariati motivi, tra cui la velocità e la mancanza di requisiti di provisioning dei dispositivi, gli sviluppatori spesso scelgono di eseguire la distribuzione in un simulatore o un emulatore preinstallato. Per i produttori di strumenti di sviluppo, la decisione di fornire un simulatore o un emulatore è dovuta a un compromesso tra velocità e compatibilità. 

Apple offre un simulatore per lo sviluppo per iOS, favorendo la velocità rispetto alla compatibilità attraverso la creazione di un ambiente meno restrittivo per l'esecuzione del codice. Questo ambiente meno restrittivo permette a Xamarin di usare il compilatore JIT per il simulatore (anziché [AOT](~/ios/internals/architecture.md) in un dispositivo) e questo significa che la build viene compilata nel codice nativo al runtime. Poiché il Mac è molto più veloce di un dispositivo, le prestazioni sono migliori.

Il simulatore usa un'icona di avvio delle applicazioni condivisa, permettendone il riutilizzo anziché doverla compilare ogni volta, nel modo richiesto nel dispositivo.

Tenendo conto delle informazioni fornite sopra, l'elenco seguente offre alcune indicazioni sui passaggi necessari quando si compila e distribuisce l'app nel simulatore per ottenere le prestazioni migliori.
 
### <a name="tips"></a>Suggerimenti

- Per le compilazioni 
  - Deselezionare l'opzione **Ottimizza immagini PNG** in Opzioni progetto. Questa ottimizzazione non è necessaria per le compilazioni nel simulatore.
  - Impostare il linker su **Non collegare**. La disabilitazione del linker è più veloce perché la sua esecuzione richiede una notevole quantità di tempo.
  - La disabilitazione dell'icona di avvio delle applicazioni condivisa tramite il flag `--nofastsim` rallenta notevolmente le compilazioni del simulatore. Rimuovere questo flag quando non è più necessario.
  - L'uso di librerie native rallenta i tempi, perché in questi casi l'eseguibile principale dell'utilità simlauncher condivisa non può essere riutilizzato ed è necessario compilare un eseguibile specifico dell'applicazione per ogni compilazione.
- Per la distribuzione
  - Mantenere sempre in esecuzione il simulatore, se possibile. L'avvio a freddo del simulatore può richiedere fino a 12 secondi.
- Suggerimenti aggiuntivi
  - Preferire la compilazione alla ricompilazione, perché la ricompilazione esegue la pulizia prima della compilazione. La pulizia può richiedere molto tempo, in quanto rimuove riferimenti che possono essere in uso.
  - Sfruttare il fatto che il simulatore non applica la sandbox. Se nel progetto sono incluse risorse di grandi dimensioni come video o altri asset, ogni volta che l'app viene avviata nel simulatore possono essere create operazioni onerose di copia dei file. Evitare queste operazioni onerose inserendo questi file nella home directory e fare riferimento ai file nell'applicazione tramite il percorso completo.  
  - In caso di dubbi, usare il flag `--time --time` per misurare la modifica

Lo screenshot seguente mostra come impostare queste opzioni per il simulatore nelle opzioni iOS:

[![](ios-build-mechanics-images/image3.png "Impostazione delle opzioni")](ios-build-mechanics-images/image3.png#lightbox)

## <a name="device-tricks"></a>Suggerimenti sul dispositivo

La distribuzione nel dispositivo è simile alla distribuzione nel simulatore, in quanto il simulatore è un piccolo subset della compilazione usata per il dispositivo iOS. La compilazione per il dispositivo richiede molti più passaggi, ma ha il vantaggio di offrire più opportunità per ottimizzare l'app.

### <a name="build-configurations"></a>Configurazioni di compilazione

Sono disponibili diverse configurazioni di compilazione per la distribuzione di app iOS. È importante comprendere al meglio ogni configurazione, per stabilire quando e perché ottimizzare il progetto.

- Debug
  - Questa è la configurazione principale da usare quando un'app è in fase di sviluppo e, di conseguenza, la velocità deve essere maggiore possibile.
- Versione
  - Le compilazioni di rilascio sono quelle fornite agli utenti e in cui le prestazioni sono un aspetto essenziale. Quando si usa la configurazione di rilascio, potrebbe essere utile usare il compilatore ottimizzatore LLVM e ottimizzare i file PNG.

 
È anche importante comprendere la relazione tra compilazione e distribuzione. I tempi di distribuzione sono una funzione delle dimensioni dell'applicazione. Un'applicazione di dimensioni maggiori richiede tempi di distribuzione più estesi. Riducendo al minimo le dimensioni dell'app, è possibile ridurre i tempi di distribuzione.

Riducendo al minimo le dimensioni dell'app, è anche possibile ridurre i tempi di compilazione. Il motivo è che la rimozione del codice dall'applicazione richiede meno tempo della compilazione nativa di codice inutilizzato. File oggetto di dimensioni minori significano un collegamento più veloce, che a sua volta crea un eseguibile di dimensioni minori con meno simboli da generare. La possibilità di risparmiare spazio offre quindi un doppio vantaggio ed è per questo che il **collegamento degli SDK** è l'opzione predefinita per tutte le compilazioni del dispositivo. 

> [!NOTE]
> Questa opzione può essere visualizzata come **Collega solo SDK** del framework o Collega solo assembly SDK, a seconda dell'IDE usato.
 

### <a name="tips"></a>Suggerimenti

- Build: 
  - La compilazione di un'unica architettura, ad esempio ARM64, è più veloce di un file binario FAT, ad esempio ARMv7 + ARM64
  - Evitare di ottimizzare i file PNG durante il debug
  - Valutare se collegare tutti gli assembly. Ottimizzare ogni assembly 
  - Disabilitare la creazione dei simboli di debug usando  `--dsym=false`. Tuttavia, tenere presente che la disabilitazione di questa opzione significa che sarà possibile aggiungere simboli alle segnalazioni di arresti anomali solo nel computer che ha compilato l'app e solo se nell'app non è stata eseguita la rimozione.

 
Alcune cose da evitare:

- File binari FAT (debug) 
- Disabilitazione del linker `--nolink` 
- Disabilitazione della rimozione 
  - Simboli `--nosymbolstrip` 
  - IL (rilascio) `--nostrip`.  
 
Suggerimenti aggiuntivi 

- Come nel caso del simulatore, preferire la compilazione alla ricompilazione 
  - Gli assembly con compilazione AOT (file oggetto) vengono memorizzati nella cache 
- Le compilazioni di debug durano più a lungo a causa dei simboli e dell'esecuzione di dsymutil e poiché finiscono per avere dimensioni maggiori, richiedono più tempo per il caricamento nei dispositivi. 
- Per impostazione predefinita, le compilazioni di rilascio eseguono una rimozione IL degli assembly. Questa operazione richiede tempo, che verrà probabilmente recuperato durante la distribuzione di un file con estensione app di dimensioni minori nel dispositivo.
- Evitare di distribuire file statici di grandi dimensioni in ogni compilazione (debug) 
  - Usare UIFileSharingEnabled (info.plist) 
    - Gli asset possono essere caricati una volta 
- In caso di dubbi, usare il flag `--time --time` per misurare la modifica

Lo screenshot seguente mostra come impostare queste opzioni per il simulatore nelle opzioni iOS:

[![](ios-build-mechanics-images/image4.png "Impostazione delle opzioni")](ios-build-mechanics-images/image4.png#lightbox)

## <a name="using-the-linker"></a>Uso del linker

Durante la compilazione dell'applicazione, mtouch usa un linker per il codice gestito, che rimuove il codice non usato dall'applicazione. In teoria, questo approccio assicura compilazioni più veloci e di dimensioni minori. Per altre informazioni sul linker, fare riferimento alla guida [Collegamento in iOS](~/ios/deploy-test/linker.md).

Quando si usa il linker, prendere in considerazione le opzioni seguenti:

- La selezione dell'opzione **Non collegare** per una compilazione del dispositivo richiede più tempo e genera anche un'app di dimensioni maggiori. 
  - Apple rifiuterà le app che hanno dimensioni maggiori del limite di dimensioni. A seconda di `MinimumOSVersion`, questo limite può essere anche solo di 60 MB. 
  - L'eseguibile nativo viene incluso. 
  - L'uso dell'opzione Non collegare è più veloce per le compilazioni del simulatore perché viene usata la compilazione JIT (anziché AOT in un dispositivo).
- Il collegamento dell'SDK è l'opzione predefinita.
- L'opzione Collega tutto può non essere sicura, in particolare se invece del proprio codice si usano, ad esempio, pacchetti NuGet o componenti esterni. Se si sceglie di non collegare gli assembly, tutto il codice di questi servizi viene incluso con l'applicazione, con il rischio di creare app di dimensioni maggiori. 
  - Tuttavia, se si sceglie **Collega tutto**, l'applicazione può arrestarsi in modo anomalo, in particolare se si usano componenti esterni. Questo problema è dovuto ad alcuni componenti che usano la reflection in certi tipi.
  - L'analisi statica e la reflection non possono essere usate insieme. 

È possibile indicare agli strumenti di mantenere tutto il codice all'interno dell'applicazione usando l'attributo [`[Preserve]`](~/ios/deploy-test/linker.md). 

Se non si ha accesso al codice sorgente o se questo è generato da uno strumento e non si vuole modificarlo, è comunque possibile collegarlo creando un file XML che descrive tutti i tipi e i membri che devono essere mantenuti. È quindi possibile aggiungere il flag `--xml={file.name}.xml` alle opzioni del progetto, per elaborare il codice esattamente come quando si usano attributi.


### <a name="partially-linking-applications"></a>Collegamento parziale delle applicazioni 

È anche possibile collegare parzialmente le applicazioni, per ottimizzare i tempi di compilazione dell'applicazione:

- Usare `Link All` e ignorare alcuni assembly 
  - Parte dell'ottimizzazione delle dimensioni dell'applicazione va perduta.
  - Non è necessario alcun accesso al codice sorgente.
  - Ad esempio, `--linkall --linkskip=fieldserviceiOS`.
 
- Usare l'opzione `Link SDK` e l'attributo `[LinkerSafe]` negli assembly necessari 
  - È necessario l'accesso al codice sorgente.
  - Indica al sistema che il collegamento dell'assembly è sicuro e l'assembly viene elaborato come se fosse un SDK Xamarin.
 
### <a name="objective-c-bindings"></a>Binding Objective-C 

- L'uso dell'attributo `[Assembly: LinkerSafe]` nei binding può ottimizzare i tempi e le dimensioni.

- SmartLink 
  - Viene effettuato sul lato nativo 
  - Usare l'attributo `[LinkWith (SmartLink=true)]`
  - Permette al linker nativo di eliminare il codice nativo dalla libreria per cui viene eseguito il collegamento. 
  - Si noti che la ricerca dinamica di simboli non funziona con questo approccio. 

## <a name="summary"></a>Riepilogo

Questa guida ha esplorato come temporizzare un'applicazione iOS e le opzioni da prendere in considerazione a seconda della configurazione e delle opzioni di compilazione del progetto. 

<!-----
# Benchmarks

## Layer 1: building again after making modifications, but _without_ cleaning should be faster 
 
The app should build a bit more quickly if you have only made changes to a subset of the libraries and you do not clean the build before re-deploying. 
 
 
 
### Clean build time 
178 seconds 
 
 
### Build again (without cleaning) after making _no changes_ 
12.5 seconds 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
3 trials: 45 seconds, 43 seconds, 43 seconds 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
 
3 trials: 45 seconds, 45 seconds, 45 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in each of the following files 
 
- ViewIOS/ImageResourcesHelper.cs 
- Sales.Native.Core.Tools/UIComponents/ListView/IListView.cs 
- View.Models/Mailing/MailingModel.cs 
- Sales.Native.Core.IOS.Ext/ServiceInterfaces/AlertDialog/Dialog.cs 
- Sales.Native.Core.Tools.IOS.Ext/BaseViews/BaseNavigationViewController.cs 
- View.Common/Services/DataTransferResult.cs 
 
45 seconds 
 
 
 
 
 
 
## Layer 2: "app thinning" aka "device specific builds" 
 
The idea of "app thinning" is that the IDE will only build the 1 architecture needed for the specific device that you're deploying to (rather than _both_ 32-bit and 64-bit architectures). 
 
As of the latest "Xamarin 4" builds, you can now enable "app thinning" in Visual Studio via the "Project Options -> iOS Build -> Enable device-specific builds" setting. 
 
Or if you prefer you can achieve a similar result by changing the "Project Options -> iOS Build -> Advanced [tab] -> Supported architectures" to select just _one_ architecture (for example ARM64 if you are developing on a 64-bit device). 
 
 
 
(Caveat: I ran the following builds in Visual Studio for Mac on the Mac rather than on the command line.) 
 
### Clean build time without "device specific builds" 
177 seconds 
 
 
 
### Clean build time _with_ "device specific builds"  
2 trials: 106 seconds, 98 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 31 seconds, 31 seconds 
 
 
* * * 
 
 
## Using the same strategy, but explicitly setting "Supported architectures" to select ARM64 _only_ (rather than using "device specific builds") 
 
(These builds were again run on the command line using `xbuild`.) 
 
 
 
### Clean build time with "Supported architectures" set to ARM64 _only_ 
2 trials: 80 seconds, 91 seconds 
 
 
 
### Build again (without cleaning) after changing 1 line in "ViewIOS/ImageResourcesHelper.cs" 
2 trials: 26 seconds, 26 seconds 
 
 
 
 
 
[1] Mac system used for testing: MacBookAir5,2 
 
- 2.0 GHz Core i7 (I7-3667U) 
 
2 Cores with hyper-threading 
 
L2 Cache (per Core): 256 KB 
L3 Cache: 4 MB 
 
- Standard MacBook soldered-in solid-state storage 
 
- 8 GB RAM 
---->


## <a name="related-links"></a>Collegamenti correlati

- [Post di blog](https://blog.xamarin.com/xamarin-ios-build-improvements/)
- [Collegamento in iOS](~/ios/deploy-test/linker.md)
- [Configurazione personalizzata del linker](~/cross-platform/deploy-test/linker.md)
