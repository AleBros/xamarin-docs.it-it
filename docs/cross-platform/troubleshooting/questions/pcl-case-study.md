---
title: Risoluzione dei problemi relativi a Tracing e flusso di dati TPL
description: 'Case study per librerie PCL: come è possibile risolvere i problemi correlati a System.Diagnostics.Tracing per il pacchetto NuGet Microsoft TPL Dataflow?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: d9aa85b946f20addb7d69c559bff68c6b1f75429
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669843"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>Case study per librerie PCL: come è possibile risolvere i problemi correlati a System.Diagnostics.Tracing per il pacchetto NuGet Microsoft TPL Dataflow?

> [!IMPORTANT]
> Questo esempio specifico del `System.Diagnostic.Tracing` non produce non più errori per impostazione predefinita nelle versioni più recenti di Xamarin. Mentre la soluzione alternativa suggerita continuerà a funzionare, si noti che sono stati corretti alcuni bug indicato nella sezione "livelli degli errori".
> Inoltre, si noti che .NET Standard è ora il modo migliore di implementazione di API .NET multipiattaforma.

## <a name="summary"></a>Riepilogo

Xamarin. IOS e xamarin. Android non implementano 100% di tutti i profili PCL che consentono come riferimenti. Per motivi di praticità pratiche in Visual Studio per Mac, Visual Studio e la gestione pacchetti NuGet, progetti Xamarin consentono l'uso dei diversi profili che dispongono unicamente _incompleto_ implementazioni. Ad esempio, xamarin. IOS né xamarin. Android attualmente include un'implementazione completa dei tipi nella libreria di classi Portabile "Tracing" dello spazio dei nomi. Questa limitazione comporta tre livelli di errori durante il tentativo di usare il valore predefinito `portable-net45+win8+wpa81` versione del pacchetto NuGet Microsoft TPL Dataflow.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Soluzione alternativa: Passare il progetto dell'app a cui fare riferimento il `portable-net45+win8+wp8+wpa81` versione della libreria del flusso di dati TPL

(Si evita così tutti e tre i livelli di errori e funziona per tutte le versioni recenti di Xamarin.)

1. Aprire il progetto di applicazione **file con estensione csproj** file in un editor di testo.

2. Trovare la riga che ha un aspetto simile al seguente:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Change `portable-net45+win8+wpa81` al `portable-net45+win8+wp8+wpa81` (`+wp8` viene aggiunto):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Descrizione

Il `portable-net45+win8+wp8+wpa81` versione della libreria non fa riferimento **System.Diagnostics.Tracing.dll** _affatto_, in modo da evitare completamente tutti i tre livelli di problemi.

### <a name="limitations"></a>Limitazioni

- Il `portable-net45+win8+wp8+wpa81` versione della libreria potrebbe non includere 100% delle funzionalità del `portable-net45+win8+wpa81` versione.

- Gestione pacchetti NuGet installa il `portable-net45+win8+wpa81` versione del pacchetto NuGet libreria di classi Portabile per impostazione predefinita, pertanto è necessario modificare manualmente il riferimento.

## <a name="details-about-the-three-layers-of-errors"></a>Dettagli relativi ai tre livelli di errori

1. Il **System.Diagnostics.Tracing.dll** assembly di facciata è attualmente assenti di tutte le versioni di Mac di xamarin. Android (non pubblico Bug 34888) e non sono presenti da tutte le versioni di xamarin. IOS (inferiore alla versione 9.0 o inferiore a XamarinVS 3.11.1443 in Windows) (risolto nella [Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Questo problema causa uno dei seguenti errori in base alla destinazione di distribuzione e del linker impostazioni:

    - Xamarin.Android.Common.targets: Errore: Eccezione durante il caricamento degli assembly: System.IO.FileNotFoundException: Impossibile caricare l'assembly ' Tracing, versione version=4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a'. Forse non esiste di Mono for Android profilo?

    - Non è stato possibile caricare file o l'assembly 'Tracing' o una delle relative dipendenze. Impossibile trovare il file specificato. (System.IO.FileNotFoundException)

    - MTOUCH: errore MT3001: Potrebbe non AOT di assembly ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll '

    - MTOUCH: errore MT2002: Non è stato possibile risolvere l'assembly: 'System.Diagnostics.Tracing, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'

2. L'oggetto corrente [Mono implementazione dei tipi in "Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) mancano alcuni overload del metodo ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Questo problema causa uno dei seguenti errori del linker quando si compila un'app Xamarin:

    - / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: errore: Errore durante l'esecuzione di attività LinkAssemblies: errore XA2006: Riferimento ai metadati di elemento 'System. void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])' (definito in ' System.Threading.Tasks.Dataflow, versione = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a') da ' System.Threading.Tasks.Dataflow, versione = 4.5.24.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a' non può essere risolto.

    - MTOUCH: errore MT2002: Non è riuscito a risolvere il riferimento "System. void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])" da "Tracing, versione version=4.0.0.0, Culture = neutral, PublicKeyToken = b03f5f7f11d50a3a"

3. L'oggetto corrente [Mono implementazione dei tipi in "Tracing"](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) attualmente è anche un' _vuota_ implementazione "fittizio" ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Qualsiasi tentativo di utilizzare questi metodi in fase di esecuzione pertanto può produrre risultati imprevisti. Per il _particolare_ case della libreria del flusso di dati TPL, Microsoft può sembrare le chiamate a `WriteEvent(System.Int32,System.Object[])` non sono essenziali per la maggior parte del comportamento della libreria, pertanto la correzione per "livello 2" ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), aggiunta vuoto le implementazioni) saranno probabilmente sufficiente per la maggior parte dei casi d'uso di Microsoft TPL Dataflow.

## <a name="questions--answers"></a>Domande e risposte

### <a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>Sono stato in grado di lasciare abilitato con il collegamento di <code>portable-net45+win8+wpa81</code> versione della libreria in versioni precedenti di xamarin. IOS o in xamarin. Android. Come ha funzionato?

#### <a name="answer"></a>Risposta

Si tratta _possibili_ per ottenere la compilazione da completato "correttamente" (con collegamento abilitato) o nelle versioni precedenti di xamarin. IOS in xamarin. Android in Mac se si include un riferimento al `System.Diagnostics.Tracing.dll` _assemblydiriferimento_ \[1\] invece _assembly di facciata_ \[2], ma sfortunatamente non è una soluzione alternativa "corretta". Gli assembly di riferimento sono concepiti per essere usato durante la compilazione solo _librerie portabili_, codice non specifico della piattaforma, ad esempio le app. Tentativo _eseguire_ il codice contenuto nell'assembly di riferimento, anziché di compilazione just-in tale, è probabile che possono generare risultati imprevisti. Sarà la soluzione corretta per il team di Mono per aggiungere il mancante `WriteEvent(System.Int32,System.Object[])` rapporto di overload per il [ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) tipo ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Per ora l'opzione migliore consiste nel passare per il `portable-net45+win8+wp8+wpa81` versione della libreria del flusso di dati TPL Microsoft come descritto nella precedente sezione soluzione alternativa.

(Per tutti gli utenti che potrebbero essere leggendo questo articolo dopo la visualizzazione di una risposta correlata meno recente, breve da StackOverflow (<https://stackoverflow.com/a/23591322/2561894>), si noti che è stata la distinzione tra gli assembly di riferimento e assembly di facciata _non_ indicato non esiste.)

**\[1\] "Assembly di riferimento" percorsi**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] percorsi degli "Assembly di facciata"**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>Sarà di aiuto se aggiunge manualmente un riferimento all'assembly di facciata "Tracing"?

_In particolare è è possibile risolvere il problema usando questi 2 passaggi?_

1. _Copia il `System.Diagnostics.Tracing.dll` assembly di facciata nella cartella del progetto dell'applicazione da una delle seguenti posizioni:_

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Aggiungere un riferimento all'assembly di facciata nel progetto dell'applicazione xamarin. IOS o xamarin. Android._

#### <a name="answer"></a>Risposta

No, non contribuisca a migliorare.

- Per xamarin. IOS 9.0 o qualsiasi versione recente di xamarin. Android in Windows, questa soluzione alternativa è rigorosamente ridondante e può causare errori di compilazione simile a "un assembly 'Tracing' con la stessa identità è già stato importato.".

- Per xamarin. IOS 8.10 o inferiore o xamarin. Android in Mac, questa soluzione consentirà ma _solo_ per il problema di assembly mancante "livello 1". Verrà _non_ risolvere gli errori del linker "livello 2", pertanto non è una soluzione completa.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>È possibile usare la [mobileengagement Tracing](https://www.nuget.org/packages/System.Diagnostics.Tracing/) per risolvere il problema?

#### <a name="answer"></a>Risposta

No, il pacchetto "Tracing" NuGet 3.0 include solo le implementazioni specifiche della piattaforma per "DNXCore50" e "netcore50". In modo esplicito _omette_ implementazioni per xamarin. Android ("MonoAndroid") e xamarin. IOS ("MonoTouch" e "xamarinios"). Ciò significa che l'installazione del pacchetto avrà _alcun effetto_ per i progetti xamarin. Android e xamarin. IOS. Il pacchetto NuGet presuppone che entrambe le piattaforme forniscono loro _proprio_ implementazione dei tipi. Questa ipotesi è "corretta" nel senso che hanno Mono _un'_ implementazione dello spazio dei nomi, ma come indicato in punti \#2 e \#3 di "Dettagli relativi ai tre livelli di errori" precedente, il implementazione è attualmente incompleta. In modo che la correzione appropriata per il team di Mono risolvere [Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) e [Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario.
