---
title: Cronologia dei rilasci
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 9a29b131af706b9dedc808a156cdfaffa4173882
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="release-history"></a>Cronologia dei rilasci

## <a name="34-october-11-2017"></a>3.4 (11 ottobre 2017)

[Scaricare v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Supporto per Xcode 9: iOS 11, macOS 10.13, 11, tvOS e watchOS 4
* Problemi con SIMD e tgmath devono ora essere corretti.
* Dati di telemetria è stata rimossa completamente

## <a name="33-august-3-2016"></a>3.3 (3 agosto 2016)

[Scaricare v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Supporto per Xcode 8 Beta 4, iOS, 10, macOS 10.12, tvOS 10 e watchOS 3.
* Aggiornato per ultimo Clang compilazione master (2016-08-02)
* [Rendere persistenti le opzioni di invio di dati di telemetria](https://twitter.com/Symbiatch/status/760373403878559744) da `sharpie pod bind` a `sharpie bind`.

## <a name="32-june-14-2016"></a>3.2 (14 giugno 2016)

[Scaricare v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Supporto per Xcode 8 Beta 1, 10 iOS, macOS 10.12, tvOS 10 e watchOS 3.

## <a name="31-may-31-2016"></a>3.1 (31 maggio 2016)

[Scaricare v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Supporto per CocoaPods 1.0
* Una maggiore affidabilità associazione CocoaPods tramite la prima compilazione nativa `.framework` quindi che l'associazione
* Copiare CocoaPods `.framework` e associazione definizione in un `Binding` directory per semplificare l'integrazione con xamarin. IOS e Xamarin.Mac progetti di associazione

## <a name="30-october-5-2015"></a>3.0 (5 ottobre 2015)

[Scaricare v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Supporto per nuove funzionalità del linguaggio Objective-C inclusi generics lightweight e supporto di valori null, così come introdotta in Xcode 7
* Supporto per iOS e Mac SDK più recenti.
* Progetto Xcode compilazione e l'analisi di supporto. È ora possibile passare un intero progetto Xcode a Sharpie obiettivo e produrrà il migliore per individuare il giusto per (ad esempio `sharpie bind Project.xcodeproj -sdk ios`).
* [CocoaPods](https://cocoapods.org) supportano! È ora possibile configurare, creare e associare CocoaPods direttamente da obiettivo Sharpie (ad esempio `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Quando si associano Framework, i moduli Clang verranno abilitati se il framework supporta, determinando l'analisi più corretta di un framework, poiché la struttura di framework viene definita dal relativo `module.modulemap`.
* Per i progetti Xcode generazione di un prodotto di framework, analizzare il prodotto anziché destinazioni prodotto intermedio come destinazioni non framework in un progetto Xcode può essere ancora le ambiguità che non possono essere risolte automaticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 marzo 2015)

[Scaricare v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* L'associazione a un'espressione di operatore binario fisso: la parte sinistra dell'espressione in modo non corretto è stata scambiata con il destro (ad esempio `1 << 0` associati in modo non corretto come `0 << 1`). Grazie a Adam Kemp per questo rilevamento.
* Risolto un problema con `NSInteger` e `NSUInteger` da associare come `int` e `uint` anziché `nint` e `nuint` su i386; `-DNS_BUILD_32_LIKE_64` ora viene passato a Clang per l'analisi di `objc/NSObjCRuntime.h` funziona come previsto in i386.
* L'architettura predefinita per Mac OS X SDK (ad esempio `-sdk macosx10.10`) è ora x86_64 anziché i386, quindi `-arch` possono essere omesse a meno che non si desidera ignorando l'impostazione predefinita.

## <a name="210-march-15-2015"></a>2.1.0 (15 marzo 2015)

[Scaricare 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc #27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): verificare `using ObjCRuntime;` viene generato quando `ArgumentSemantic` viene utilizzato.
* [bxc #27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): verificare `using System.Runtime.InteropServices;` viene generato quando `DllImport` viene utilizzato.
* [bxc #27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): predefinito `DllImport` per il caricamento dei simboli da `__Internal`.
* [bxc 27848 #](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): ignorare dichiarato forward le dichiarazioni di contenitore Objective-C.
* [bxc #27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): associare tipi di protocollo con una qualificazione solo come interfacce concrete (`id<Foo>` come `Foo` anziché `Foundation.NSObject<Foo>`).
* [bxc #28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): associare `UInt32`, `UInt64`, e `Int64` valori letterali come `Int32` per eliminare il `u` e/o `uL` suffissi quando i valori in modo sicuro possono essere contenuti in `Int32`.
* [bxc #28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): correggere enum mapping di nomi quando si avvia il nome nativo originale con un `k` prefisso.
* `sizeof` Verranno valutate in Clang C espressioni il cui tipo di argomento non è mappato a un tipo primitivo di c# e associate come un valore letterale integer per evitare di generare in c# non valido.
* Correggere la sintassi Objective-C per le proprietà il cui tipo è un blocco (codice Objective-C viene visualizzato nei commenti sopra dichiarazioni associate).
* Associare tipi cariati come relativo tipo originale (`int[]` che vengono a `int*` durante l'analisi semantica in Clang ma associarlo dell'originale come scritto `int[]` invece).

Molto di Dave Dunkin grazie per aver molti dei bug corretti in questa versione del punto di reporting.

## <a name="200-march-9-2015"></a>2.0.0: 9 marzo 2015

[Scaricare v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Obiettivo Sharpie 2.0 è una versione principale che offre un driver basati su Clang migliorato e parser e un nuovo motore di associazione di base NRefactory. Questi componenti migliorati prevedere _molto_ migliori associazioni, soprattutto per l'associazione di tipo. Sono stati apportati molti altri miglioramenti interni all'obiettivo Sharpie che restituirà molte funzionalità visibile dall'utente nelle versioni future.

Obiettivo Sharpie 2.0 si basa su Clang 3.6.1.

### <a name="type-binding-improvements"></a>Miglioramenti di associazione di tipo

* Blocchi di Objective-C sono ora supportati. Inclusi i blocchi anonimo/inline e blocchi denominati tramite `typedef`. Blocchi anonimi verranno associati come `System.Action` o `System.Func` delegati, mentre saranno associati blocchi denominati fortemente denominato `delegate` tipi.

* È un miglioramento euristica di denominazione per le enumerazioni anonime immediatamente preceduti da un `typedef` risoluzione, ad esempio in un tipo integrale builtin `long` o `int`.

* Puntatori di C sono associati a questo punto come c# `unsafe` puntatori anziché `System.IntPtr`. Il risultato è maggiore chiarezza nell'associazione per quando si desidera attivare i parametri del puntatore in `out` o `ref` parametri. Non è possibile dedurre se un parametro deve essere sempre `out` o `ref`, in modo che il puntatore viene mantenuto nell'associazione per consentire il controllo più semplice.

* Un'eccezione all'associazione puntatore sopra è quando viene rilevato un puntatore di 2-numero di dimensioni a un oggetto Objective-C come parametro. In questi casi, la convenzione è predominante e verrà associato il parametro come `out` (ad esempio `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Verificare l'attributo

È spesso che associazioni prodotte dall'obiettivo Sharpie verranno ora annotate con il `[Verify]` attributo. Questi attributi indicano che è necessario _verificare_ che Sharpie obiettivo è la cosa corretta confrontando l'associazione con la dichiarazione originale di C/Objective-C (che verrà fornita in un commento sopra la dichiarazione di associazione).

È la verifica _consigliato_ per tutte le dichiarazioni associate, ma è più probabile _obbligatorio_ per le dichiarazioni annotate con il `[Verify]` attributo. Infatti, in molti casi, non c'è sufficiente metadati nel codice sorgente nativo originale per dedurre come produrre più di un'associazione. Potrebbe essere necessario fare riferimento a documentazione o commenti del codice all'interno dei file di intestazione per prendere la decisione migliore di associazione.

Vedere il [verificare gli attributi](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) documentazione per maggiori dettagli.

### <a name="other-notable-improvements"></a>Altri importanti miglioramenti

* `using` le istruzioni vengono ora generate in base ai tipi associati. Ad esempio, se un `NSURL` è stato associato un `using Foundation;` verrà generata anche istruzione.

* `struct` e `union` dichiarazioni verranno associate, tramite il `[FieldOffset]` stratagemma per le unioni.

* Verranno associati correttamente i valori enum con inizializzatori espressione costante; l'espressione completa viene convertito in c#.

* Blocchi e i metodi Variadic ora sono associati.

* Framework sono ora supportati tramite il `-framework` opzione. Vedere la documentazione su [associazione Framework nativo](http://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks) per altri dettagli.

* Codice sorgente Objective-C verrà rilevata automaticamente a questo punto, che deve eliminare la necessità di passare `-ObjC` o `-xobjective-c` per Clang manualmente.

* Utilizzo dei moduli clang (`@import`) viene ora rilevata automaticamente, che deve eliminare la necessità di passare `-fmodules` per Clang manualmente per le librerie che utilizzano il nuovo modulo di supporto in Clang.

* L'API unificata Xamarin è la destinazione di associazione predefinita; Utilizzare il `-classic` opzione per l'API classica solo 32 bit di destinazione.

### <a name="notable-bug-fixes"></a>Correzioni di bug importanti

* Correggere `instancetype` associazione quando utilizzato in una categoria di Objective-C
* Assegnare un nome completamente categorie Objective-C
* Prefisso protocolli Objective-C con `I` (ad esempio `INSCopying` anziché `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 21 dicembre 2014

[Scaricare v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Piccole correzioni di bug.

## <a name="111-december-15-2014"></a>1.1.1: 15 dicembre 2014

[Scaricare 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 è il primo rilascio principale dopo 1,5 anni di uso interno e sviluppo Xamarin seguente l'anteprima iniziale dell'obiettivo Sharpie in aprile 2013. Questa versione è il primo in genere considerato stabile e può essere usato per un'ampia gamma di librerie native, che presenta un nuovo back-end Clang.

