---
title: Risolvere i problemi relativi al flusso di dati System. Diagnostics. Tracing e TPL
description: 'Case study per librerie PCL: come è possibile risolvere i problemi correlati a System.Diagnostics.Tracing per il pacchetto NuGet Microsoft TPL Dataflow?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: 09aef14efdce93e28326deb78292da98f1969ea1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521558"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Case study per librerie PCL: come è possibile risolvere i problemi correlati a System.Diagnostics.Tracing per il pacchetto NuGet Microsoft TPL Dataflow?

> [!IMPORTANT]
> Questo particolare esempio di `System.Diagnostic.Tracing` non produce più errori per impostazione predefinita nelle versioni più recenti di Novell. Sebbene la soluzione alternativa suggerita continui a funzionare, si noti che alcuni dei bug indicati nella sezione "livelli di errori" sono stati corretti.
> Si noti inoltre che .NET Standard è ora il modo migliore per implementare API .NET multipiattaforma.

## <a name="summary"></a>Riepilogo

Novell. iOS e Novell. Android non implementano il 100% di ogni profilo PCL che consentono come riferimenti. Per praticità in Visual Studio per Mac, Visual Studio e gestione pacchetti NuGet, i progetti Novell consentono di usare diversi profili che hanno solo implementazioni _incomplete_ . Ad esempio, né Novell. iOS né Novell. Android includono attualmente un'implementazione completa dei tipi nello spazio dei nomi PCL "System. Diagnostics. Tracing". Questa limitazione comporta tre livelli di errori quando si prova a usare la versione `portable-net45+win8+wpa81` predefinita del pacchetto NuGet Microsoft TPL Dataflow.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Soluzione alternativa: Cambiare il progetto dell'app in modo `portable-net45+win8+wp8+wpa81` che faccia riferimento alla versione della libreria del flusso di flussi di lavoro TPL

In questo modo si evitano tutti e tre i livelli di errori e funziona per tutte le versioni recenti di Novell.

1. Aprire il file Project **. csproj** dell'applicazione in un editor di testo.

2. Trovare la riga simile alla seguente:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Modifica `portable-net45+win8+wpa81` in `portable-net45+win8+wp8+wpa81` (`+wp8` aggiunta):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Spiegazione

La `portable-net45+win8+wp8+wpa81` versione della libreria non fa riferimento a **System. Diagnostics. Tracing. dll**, quindi evita completamente tutti i tre livelli di problemi.

### <a name="limitations"></a>Limitazioni

- La `portable-net45+win8+wp8+wpa81` versione della libreria potrebbe non includere il 100% delle funzionalità `portable-net45+win8+wpa81` della versione.

- Per impostazione predefinita, gestione pacchetti `portable-net45+win8+wpa81` NuGet installa la versione del pacchetto NuGet di PCL, quindi è necessario modificare manualmente il riferimento.

## <a name="details-about-the-three-layers-of-errors"></a>Dettagli sui tre livelli di errori

1. L'assembly di facciata **System. Diagnostics. Tracing. dll** è attualmente assente da tutte le versioni Mac di Novell. Android (bug non pubblico 34888) ed è assente da tutte le versioni di Novell. iOS inferiori a 9,0 (o inferiori a XamarinVS 3.11.1443 in Windows) (fisso in [Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Questo problema provocherà uno degli errori seguenti a seconda della destinazione di distribuzione e delle impostazioni del linker:

    - Xamarin.Android.Common.targets: Errore: Eccezione durante il caricamento degli assembly: System.IO.FileNotFoundException: Non è stato possibile caricare l'assembly ' System. Diagnostics. Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a '. Forse non esiste nel profilo mono per Android?

    - Non è stato possibile caricare il file o l'assembly ' System. Diagnostics. Tracing ' o una delle relative dipendenze. Impossibile trovare il file specificato. (System.IO.FileNotFoundException)

    - MTOUCH: errore MT3001: Non è stato possibile AOT l'assembly '/Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - MTOUCH: errore MT2002: Non è stato possibile risolvere l'assembly: 'System.Diagnostics.Tracing, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'

2. Nell' [implementazione mono corrente dei tipi in "System. Diagnostics. Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) mancano alcuni overload del metodo ([bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Questo problema provocherà uno dei seguenti errori del linker durante la compilazione di un'app Novell:

    - /Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: errore: Errore durante l'esecuzione dell'attività LinkAssemblies: errore XA2006: Riferimento all'elemento dei metadati ' System. void System. Diagnostics. Tracing. EventSource:: WriteEvent (System. Int32, System. Object [])' (definito in ' System. Threading. Tasks. Dataflow, Version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ') non è stato possibile risolvere da' System. Threading. Tasks. Dataflow, Version = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a '.

    - MTOUCH: errore MT2002: Non è stato possibile risolvere il riferimento "System. void System. Diagnostics. Tracing. EventSource:: WriteEvent (System. Int32, System. Object [])" da "System. Diagnostics. Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"

3. Anche l' [implementazione mono corrente dei tipi in "System. Diagnostics. Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) è attualmente un'implementazione "fittizia" _vuota_ ([bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Qualsiasi tentativo di usare questi metodi in fase di esecuzione potrebbe produrre risultati imprevisti. Per il caso particolare della libreria di flussi di _dati_ Microsoft TPL, sembra che le chiamate `WriteEvent(System.Int32,System.Object[])` a non siano essenziali per la maggior parte del comportamento della libreria, quindi la correzione per "livello 2" ([bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), aggiunta di implementazioni vuote) sarà probabilmente sufficiente per la maggior parte dei casi d'uso di Microsoft TPL Dataflow.

## <a name="questions--answers"></a>Domande & risposte

### <a name="i-was-able-to-leave-linking-enabled-with-the-portable-net45win8wpa81-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>Sono riuscito a lasciare il collegamento abilitato con la `portable-net45+win8+wpa81` versione della libreria nelle versioni precedenti di Novell. iOS o in Novell. Android. In che modo funziona?

#### <a name="answer"></a>Risposta

Se si include un riferimento all' `System.Diagnostics.Tracing.dll` _assembly_ \[di riferimento 1\],èpossibilefareinmodochelacompilazionevengacompletatacorrettamente(concollegamentoabilitato)nelleversioniprecedentidiNovell.iOSoinNovell.AndroidinMac.anziché l' _assembly_ \[di facciata 2], ma sfortunatamente non si tratta di una soluzione "corretta". Gli assembly di riferimento devono essere usati solo per la compilazione di _librerie_portabili, non per il codice specifico della piattaforma, ad esempio le app. Il tentativo di _eseguire_ il codice contenuto negli assembly di riferimento (anziché semplicemente compilarlo) potrebbe produrre risultati imprevisti. La correzione corretta sarà destinata al team mono per aggiungere l'overload `WriteEvent(System.Int32,System.Object[])` mancante [`EventSource`](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) al tipo ([bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Per ora, l'opzione migliore consiste nel passare alla `portable-net45+win8+wp8+wpa81` versione della libreria del flusso di flussi di Microsoft TPL, come illustrato nella sezione precedente relativa alla soluzione alternativa.

Per chiunque stia leggendo questo articolo dopo aver visto una risposta precedente e più breve da StackOverflow (<https://stackoverflow.com/a/23591322/2561894>), si noti che la distinzione tra gli assembly di riferimento e l'assembly di facciata _non_ è stata citata.

**\[1\] località "assembly di riferimento"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] posizioni "assembly di facciata"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>Sarà utile se si aggiunge manualmente un riferimento all'assembly di facciata "System. Diagnostics. Tracing"?

_In particolare, è possibile risolvere il problema usando questi 2 passaggi?_

1. _Copiare l' `System.Diagnostics.Tracing.dll` assembly di facciata nella cartella del progetto dell'applicazione da uno dei percorsi seguenti:_

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Aggiungere un riferimento all'assembly di facciata nel progetto di applicazione Novell. iOS o Novell. Android._

#### <a name="answer"></a>Risposta

No, questa operazione non può essere utile.

- Per Novell. iOS 9,0 o una versione recente di Novell. Android in Windows, questa soluzione alternativa è strettamente ridondante e potrebbe causare errori di compilazione simili a "System. Diagnostics. Tracing ' con la stessa identità è già stato importato".

- Per Novell. iOS 8,10 o versioni precedenti o per Novell. Android in Mac, questa soluzione alternativa può essere utile, ma _solo_ per il problema dell'assembly mancante di "livello 1". Non risolverà gli errori del linker "livello 2", quindi non si tratta di una soluzione completa.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>È possibile usare il [pacchetto NuGet System. Diagnostics. Tracing](https://www.nuget.org/packages/System.Diagnostics.Tracing/) per risolvere il problema?

#### <a name="answer"></a>Risposta

No, il pacchetto NuGet 3,0 "System. Diagnostics. Tracing" include solo implementazioni specifiche della piattaforma per "DNXCore50" e "netcore50". _Omette_ esplicitamente le implementazioni per Novell. Android ("monoandroid") e Novell. iOS ("MonoTouch" e "Xamarin"). Ciò significa che l'installazione del pacchetto _non avrà alcun effetto_ per i progetti Novell. Android e Novell. iOS. Il pacchetto NuGet presuppone che entrambe le piattaforme forniscano _una propria_ implementazione dei tipi. Questo presupposto è "corretto" nel senso che mono ha _un'_ implementazione dello spazio dei nomi, ma come descritto in punti \#2 e \#3 di "dettagli sui tre livelli di errore", l'implementazione è attualmente incompleto. Quindi, la correzione corretta sarà per il team mono per la risoluzione del [bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) e del [bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Fasi successive

Per ulteriore assistenza, per contattarci o se il problema persiste anche dopo aver utilizzato le informazioni sopra riportate, vedere [quali sono le opzioni di supporto disponibili per Novell?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché su come archiviare un nuovo bug se necessario .
