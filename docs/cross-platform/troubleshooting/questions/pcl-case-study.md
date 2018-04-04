---
title: 'Case study di libreria di classi Portabile: come è possibile risolvere i problemi correlati alla System.Diagnostics.Tracing per il pacchetto NuGet di flussi di dati TPL Microsoft?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 04814b78fd035005aabd8b9229d36bbda17ba140
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Case study di libreria di classi Portabile: come è possibile risolvere i problemi correlati alla System.Diagnostics.Tracing per il pacchetto NuGet di flussi di dati TPL Microsoft?

## <a name="summary"></a>Riepilogo

Xamarin. IOS e xamarin non implementano 100% di tutti i profili che consentono come riferimenti PCL. Per motivi di praticità pratiche in Visual Studio per Mac, Visual Studio e gestione pacchetti NuGet, progetti di Xamarin consentono l'uso di diversi profili che hanno solo _incompleto_ implementazioni. Ad esempio, xamarin né xamarin attualmente include un'implementazione completa dei tipi nella libreria di classi Portabile "System.Diagnostics.Tracing" spazio dei nomi. Questa limitazione comporta 3 livelli di errori durante il tentativo di utilizzare il valore predefinito `portable-net45+win8+wpa81` versione del pacchetto NuGet di flussi di dati TPL Microsoft.


## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Soluzione alternativa: Passare il progetto di app per fare riferimento il `portable-net45+win8+wp8+wpa81` versione della libreria del flusso di dati TPL

(Questo evita di tutti i 3 livelli di errori e può essere usato per tutte le versioni recenti di Xamarin).

1. Aprire il progetto di applicazione `.csproj` file in un editor di testo.

2. Trovare la riga che è simile al seguente:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Modificare `portable-net45+win8+wpa81` in `portable-net45+win8+**wp8**+wpa81`:

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Descrizione

Il `portable-net45+win8+wp8+wpa81` versione della libreria non fa riferimento a "System.Diagnostics.Tracing.dll" _affatto_, in modo da evitare completamente tutti i 3 livelli dei problemi.

### <a name="limitations"></a>Limitazioni

- Il `portable-net45+win8+wp8+wpa81` versione della libreria potrebbe non includere 100%, la funzionalità di `portable-net45+win8+wpa81` versione.

- Installa Gestione pacchetti NuGet il `portable-net45+win8+wpa81` versione del pacchetto NuGet PCL per impostazione predefinita, è necessario modificare manualmente il riferimento.




## <a name="details-about-the-3-layers-of-errors"></a>Dettagli relativi a 3 livelli di errori

1. Il `System.Diagnostics.Tracing.dll` assembly di facciata è assente da tutte le versioni di Mac di xamarin (pubblici Bug 34888) e non sono presenti da tutte le versioni di xamarin. IOS inferiore a 9.0 (o inferiore a XamarinVS 3.11.1443 in Windows) (corretti in [Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Questo problema causa uno degli errori seguenti a seconda della destinazione di distribuzione e del linker impostazioni:

    - > Xamarin.Android.Common.targets: Errore: eccezione durante il caricamento degli assembly: System.IO. FileNotFoundException: Impossibile caricare l'assembly ' System.Diagnostics.Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a ". Probabilmente non esiste nel Mono per Android profilo?

    - > Impossibile caricare file o l'assembly 'System.Diagnostics.Tracing' o una delle sue dipendenze. Impossibile trovare il file specificato. (System.IO.FileNotFoundException)

    - > MTOUCH: errore MT3001: impossibile non AOT l'assembly ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - > MTOUCH: errore MT2002: Impossibile risolvere l'assembly: ' System.Diagnostics.Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a'



2. Corrente [implementazione Mono di tipi in "System.Diagnostics.Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) mancano alcuni overload del metodo ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Questo problema genererà uno dei seguenti errori del linker quando si compila un'app Xamarin:

    - > / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: errore: errore durante l'esecuzione di attività LinkAssemblies: errore XA2006: riferimento all'elemento dei metadati ' System. void EventSource:: WriteEvent(System.Int32,System.Object[])' (definito in ' System.Threading.Tasks.Dataflow, versione = 4.5.24.0, delle impostazioni cultura = neutral, PublicKeyToken = b03f5f7f11d50a3a') da ' System.Threading.Tasks.Dataflow, versione = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a' non è stato possibile risolvere.

    - > MTOUCH: errore MT2002: Impossibile risolvere il riferimento "System. void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])" da "System.Diagnostics.Tracing, Version = 4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"


3. Corrente [implementazione Mono di tipi in "System.Diagnostics.Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) è attualmente un _vuoto_ implementazione "fittizio" ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Qualsiasi tentativo di usare questi metodi in fase di esecuzione può pertanto generare risultati imprevisti. Per il _particolare_ caso della libreria del flusso di dati TPL Microsoft, sembra che le chiamate a `WriteEvent(System.Int32,System.Object[])` non sono essenziali per la maggior parte del comportamento della libreria, pertanto la correzione per "livello 2" ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), aggiunta vuoto le implementazioni) verranno likey essere sufficiente per la maggior parte dei casi di utilizzo del flusso di dati TPL Microsoft.




## <a name="questions--answers"></a>Domande e risposte


###<a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>"È stato possibile lasciare il collegamento abilitato con il <code>portable-net45+win8+wpa81</code> versione della libreria in versioni precedenti di xamarin. IOS o in xamarin. Come ha funzionato?"

#### <a name="answer"></a>Risposta

È _possibili_ per ottenere la compilazione a completo "completato" (con collegamento abilitato) nelle versioni precedenti di xamarin. IOS o in xamarin su Mac se si include un riferimento di `System.Diagnostics.Tracing.dll` _fa riferimento l'assembly_ \[1\] anziché _assembly di facciata_ \[2], ma purtroppo non è una soluzione alternativa "corretta". Gli assembly di riferimento sono concepiti per essere utilizzati durante la compilazione solo _librerie portabili_, codice non specifico della piattaforma come app. Il tentativo di _eseguire_ il codice contenuto nell'assembly di riferimento, anziché solo compilazione su di esso, è probabile che per produrre risultati imprevisti. La soluzione corretta sarà per il team Mono aggiungere la mancante `WriteEvent(System.Int32,System.Object[])` overload per il [ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) tipo ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Per ora la soluzione migliore consiste nel passare per il `portable-net45+win8+wp8+wpa81` versione della libreria del flusso di dati TPL Microsoft come descritto nella precedente sezione della soluzione.

(Per tutti gli utenti potrebbero essere leggendo questo articolo dopo la visualizzazione di una risposta precedente e breve correlata da StackOverflow (<http://stackoverflow.com/a/23591322/2561894>), si noti che è stata la distinzione tra gli assembly di riferimento e assembly di facciata _non_ indicato non esiste.)


**\[1\] "Assembly di riferimento" percorsi**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] percorsi "Assembly di facciata"**

Windows: <code>C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\<strong>Facades</strong>\System.Diagnostics.Tracing.dll</code>

Mac (Mono): <code>/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/<strong>Facades</strong>/System.Diagnostics.Tracing.dll</code>


###<a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>"Verrà utile se si aggiunge manualmente un riferimento all'assembly di facciata"System.Diagnostics.Tracing"?"

In particolare è possibile risolvere il problema utilizzando questi 2 passaggi?

1. Copia il `System.Diagnostics.Tracing.dll` assembly di facciata nella cartella del progetto di applicazione da uno dei seguenti percorsi:

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. Aggiungere un riferimento all'assembly di facciata nel progetto di applicazione di xamarin. IOS o xamarin.

#### <a name="answer"></a>Risposta

No, ciò non consentirà.

- Per xamarin. IOS 9.0 o una versione recente di xamarin in Windows, questa soluzione è rigorosamente ridondante e potrebbe causare errori di compilazione simile a "un assembly 'System.Diagnostics.Tracing' con la stessa identità è già stato importato.".

- Per xamarin 8.10 minore o uguale a o xamarin su Mac, questa soluzione consentirà ma _solo_ per il problema di assembly mancante "livello 1". Si verifica un _non_ risolvere gli errori del linker "livello 2", pertanto non è una soluzione completa.


###"È possibile utilizzare il <a href="https://www.nuget.org/packages/System.Diagnostics.Tracing/">pacchetto System.Diagnostics.Tracing NuGet</a> per risolvere il problema?"

#### <a name="answer"></a>Risposta

No, il pacchetto di "System.Diagnostics.Tracing" NuGet 3.0 include solo le implementazioni specifiche della piattaforma per "DNXCore50" e "netcore50". È in modo esplicito _omette_ implementazioni per xamarin ("MonoAndroid") e xamarin. IOS ("MonoTouch" e "xamarinios"). Ciò significa che l'installazione del pacchetto avrà _alcun effetto_ per i progetti di xamarin e xamarin. IOS. Il pacchetto NuGet si presuppone che entrambe le piattaforme fornire loro _proprio_ implementazione dei tipi. Questa ipotesi è "corretta" nel senso che hanno Mono _un_ implementazione dello spazio dei nomi, ma come indicato in punti \#2 e \#3 "Dettagli i 3 livelli di errori" in precedenza, l'implementazione non è attualmente completa. In modo che sia la correzione appropriata per il team di risolvere Mono [Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) e [Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).


## <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, contattare Microsoft, o se il problema rimane anche dopo l'utilizzo di queste informazioni, vedere [quali opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, i suggerimenti, nonché come Se necessario, archiviare un nuovo bug.
