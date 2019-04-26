---
title: Cronologia delle versioni Sharpie obiettivo
description: Questo documento descrive la cronologia del rilascio di Sharpie obiettivo, lo strumento utilizzato per automatizzare la creazione di C# binding a codice Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 03e4a5ac8906d2593cbdf3c15f6b2d1f4a2c6d19
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199649"
---
# <a name="objective-sharpie-release-history"></a>Cronologia delle versioni Sharpie obiettivo

## <a name="34-october-11-2017"></a>3.4 (valide all'11 ottobre 2017)

[Scaricare v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Supporto per Xcode 9: iOS 11, macOS 10.13, 11, tvOS e watchOS 4
* A questo punto dovrebbero essere risolto i problemi relativi a estensioni SIMD e tgmath
* I dati di telemetria è stata rimossa completamente

## <a name="33-august-3-2016"></a>3.3 (3 agosto 2016)

[Scaricare v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Supporto per Xcode 8 Beta 4, 10 iOS, macOS 10.12, tvOS 10 e watchOS 3.
* Aggiornato per ultimo Clang compilazione master (2016-08-02)
* [Rendere persistenti le opzioni di invio di dati di telemetria](https://twitter.com/Symbiatch/status/760373403878559744) dal `sharpie pod bind` a `sharpie bind`.

## <a name="32-june-14-2016"></a>3.2 (14 giugno 2016)

[Scaricare v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Supporto per Xcode 8 Beta 1, 10 iOS, macOS 10.12, tvOS 10 e watchOS 3.

## <a name="31-may-31-2016"></a>3.1 (31 maggio 2016)

[Scaricare v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Supporto per CocoaPods 1.0
* Maggiore affidabilità di associazione CocoaPods creando prima nativo `.framework` quindi che l'associazione
* Copiare CocoaPods `.framework` e il binding definizione in un `Binding` directory per semplificare l'integrazione con i progetti di binding di xamarin. IOS e xamarin. Mac

## <a name="30-october-5-2015"></a>3.0 (5 ottobre 2015)

[Scaricare v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Supporto per nuove funzionalità del linguaggio Objective-C inclusi generics leggero e supporto di valori null, come introdotto in Xcode 7
* Supporto per iOS e Mac SDK più recente.
* Progetto Xcode, compilazione e l'analisi di supporto. È ora possibile passare un progetto Xcode completo a Sharpie obiettivo che sia eseguita meglio individuare il giusto per (ad esempio `sharpie bind Project.xcodeproj -sdk ios`).
* [CocoaPods](https://cocoapods.org) supportano! È ora possibile configurare, compilare e associare CocoaPods direttamente dall'obiettivo Sharpie (ad esempio `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Quando si associano i Framework, i moduli Clang verranno abilitati se il framework di supportarle, causando l'analisi più corretta di un framework, poiché la struttura di framework viene definita dal relativo `module.modulemap`.
* Per i progetti Xcode compilare un prodotto di framework, analizzare tale prodotto anziché destinazioni prodotto intermedio come destinazioni non framework in un progetto Xcode potrebbero ancora contenere le ambiguità che non possono essere risolte automaticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 marzo 2015)

[Scaricare v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Associazione di espressioni di operatore binario predefinito: la parte sinistra dell'espressione in modo non corretto è stata scambiata con a destra (ad esempio `1 << 0` erroneamente associati come `0 << 1`). Grazie a Adam Kemp per rintracciando ciò.
* Risolto un problema con `NSInteger` e `NSUInteger` da associare come `int` e `uint` anziché `nint` e `nuint` su i386; `-DNS_BUILD_32_LIKE_64` ora viene passato a Clang per rendere l'analisi `objc/NSObjCRuntime.h` funziona come previsto in i386.
* L'architettura predefinita per gli SDK di Mac OS X (ad esempio `-sdk macosx10.10`) è ora x86_64 anziché i386, quindi `-arch` può essere omesso solo se si desidera la ignorando l'impostazione predefinita.

## <a name="210-march-15-2015"></a>2.1.0 (15 marzo 2015)

[Scaricare 2.1.0 in](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxC#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Assicurarsi `using ObjCRuntime;` viene generato quando `ArgumentSemantic` viene usato.
* [bxC#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Assicurarsi `using System.Runtime.InteropServices;` viene generato quando `DllImport` viene usato.
* [bxC#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): Default `DllImport` per il caricamento dei simboli da `__Internal`.
* [bxC#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): Ignorare le dichiarazioni di contenitore Objective-C dichiarazione con prototipo.
* [bxC#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): Associare i tipi di protocollo con una qualificazione singolo come interfacce concrete (`id<Foo>` come `Foo` invece di `Foundation.NSObject<Foo>`).
* [bxC#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): Associare `UInt32`, `UInt64`, e `Int64` valori letterali come `Int32` per eliminare il `u` e/o `uL` i suffissi quando i valori in modo sicuro possono essere inserito in `Int32`.
* [bxC#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): Correggere enum mapping di nomi se il nome nativo originale inizia con un `k` prefisso.
* `sizeof` Espressioni C il cui tipo di argomento non è mappato a un C# tipo primitivo verrà valutata in Clang e associato come un valore letterale integer per evitare la generazione non valido C#.
* Correggere la sintassi di Objective-C per le proprietà il cui tipo è un blocco (codice Objective-C viene visualizzato nei commenti sopra le dichiarazioni di associazione).
* Associare tipi cariati a quelli del tipo originale (`int[]` che vengono a `int*` durante l'analisi semantica in Clang, ma eseguirne l'associazione dell'originale come scritto `int[]` invece).

Grazie mille a Dave Dunkin per molti dei bug corretti in questa versione del punto di reporting.

## <a name="200-march-9-2015"></a>2.0.0: 9 marzo 2015

[Scaricare v2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Obiettivo Sharpie 2.0 è una versione principale che offre un driver basate su Clang migliorato e parser e un nuovo motore di binding basati su NRefactory. Questi componenti migliorati prevedere _gran parte_ meglio le associazioni, in particolare correlati all'associazione dei tipi. Sono stati apportati molti altri miglioramenti interni a Sharpie obiettivo che restituirà molte funzionalità visibile all'utente nelle versioni future.

Obiettivo Sharpie 2.0 si basa su Clang 3.6.1.

### <a name="type-binding-improvements"></a>Miglioramenti di associazione di tipo

* Blocchi di Objective-C sono ora supportati. Sono inclusi i blocchi anonimo/inline e i blocchi denominati tramite `typedef`. Blocchi anonimi verranno associati come `System.Action` oppure `System.Func` delegati, mentre i blocchi denominati verranno associati un nome come sicuro `delegate` tipi.

* C'è un approccio euristico dei nomi migliorato per le enumerazioni anonimi sono immediatamente preceduto da un `typedef` , ad esempio la risoluzione a un tipo integrale builtin `long` o `int`.

* I puntatori di C sono associati a questo punto come C# `unsafe` puntatori anziché `System.IntPtr`. Ciò comporta maggiore chiarezza nell'associazione per quando si desidera trasformare i parametri di puntatore nella `out` o `ref` parametri. Non è possibile dedurre se un parametro deve essere sempre `out` o `ref`, in modo che il puntatore viene conservato nell'associazione per consentire il controllo più semplice.

* Un'eccezione all'associazione al puntatore precedente è quando viene rilevato un puntatore alla classificazione 2 a un oggetto Objective-C come parametro. In questi casi, è predominante convenzione e verrà associato il parametro come `out` (ad esempio `NSError **error` → `out NSError error`).

### <a name="verify-attribute"></a>Verificare l'attributo

Si potrà spesso notare che associazioni prodotte da Sharpie obiettivo verranno ora annotate con il `[Verify]` attributo. Questi attributi indicano che occorre _verificare_ che obiettivo Sharpie ha l'aspetto corretto confrontando l'associazione con la dichiarazione di C/Objective-C originale (che verrà fornita in un commento sopra la dichiarazione di associazione).

È la verifica _consigliato_ per tutte le dichiarazioni di associazione, ma è più probabile _obbligatorio_ per le dichiarazioni annotati con il `[Verify]` attributo. Questo avviene perché in molte situazioni, non è sufficiente metadati nel codice sorgente nativa originale per dedurre come meglio produrre un'associazione. Potrebbe essere necessario fare riferimento a documentazione o commenti del codice all'interno di file di intestazione per rendere il processo decisionale dell'associazione.

Vedere le [verificare gli attributi](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) informazioni più dettagliate.

### <a name="other-notable-improvements"></a>Altri importanti miglioramenti

* `using` le istruzioni vengono ora generate basati sui tipi associati. Ad esempio, se un' `NSURL` è stata associata una `using Foundation;` informativa verrà generato anche.

* `struct` e `union` dichiarazioni verranno associate, utilizzando il `[FieldOffset]` trucco per le unioni.

* Verranno associati correttamente i valori di enumerazione con gli inizializzatori di espressione costante; l'espressione completa viene convertita in C#.

* Blocchi e i metodi Variadic vengono ora associati.

* Framework sono ora supportati tramite il `-framework` opzione. Vedere la documentazione sul [associazione di Framework nativi](https://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks) per altri dettagli.

* Codice sorgente di Objective-C saranno rilevati automaticamente a questo punto, che deve eliminare la necessità di passare `-ObjC` o `-xobjective-c` per Clang manualmente.

* Clang utilizzo dei moduli (`@import`) viene ora rilevato automaticamente, che deve eliminare la necessità di passare `-fmodules` da Clang manualmente per le raccolte che usano il nuovo supporto di modulo in Clang.

* API unificata di Xamarin è ora la destinazione di associazione predefinita. usare il `-classic` opzione per l'API classica solo 32 bit di destinazione.

### <a name="notable-bug-fixes"></a>Importanti correzioni di bug

* Correggere `instancetype` associazione quando utilizzata in una categoria di Objective-C
* Assegnare un nome completamente le categorie di Objective-C
* Protocolli di Objective-C con prefisso `I` (ad esempio `INSCopying` invece di `NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35: 21 dicembre 2014

[Scaricare v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correzioni di bug minori.

## <a name="111-december-15-2014"></a>1.1.1: 15 dicembre 2014

[Scaricare 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 era il primo rilascio importante dopo 1,5 anni di uso interno e lo sviluppo di Xamarin dopo l'anteprima iniziale di obiettivo Sharpie ad aprile 2013. Questa versione è il primo a essere in genere considerate stabili e utilizzabile per un'ampia gamma di librerie native, che presenta un nuovo back-end Clang.

