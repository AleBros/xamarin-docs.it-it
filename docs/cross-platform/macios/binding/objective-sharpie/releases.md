---
title: Cronologia delle versioni di Objective Sharpe
description: Questo documento descrive la cronologia delle versioni di Objective Sharpie, lo strumento usato per automatizzare la creazione C# di associazioni nel codice Objective-C.
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 86c9f46064b66dc31f805e830309cb061a78a2c8
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509644"
---
# <a name="objective-sharpie-release-history"></a>Cronologia delle versioni di Objective Sharpe

## <a name="34-october-11-2017"></a>3,4 (11 ottobre 2017)

[Scarica v 3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* Supporto per Xcode 9: iOS 11, macOS 10,13, tvOS 11 e watchos 4
* I problemi con SIMD e tgmath dovrebbero ora essere corretti
* La telemetria è stata rimossa completamente

## <a name="33-august-3-2016"></a>3,3 (3 agosto 2016)

[Scarica v 3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* Supporto per Xcode 8 Beta 4, iOS 10, macOS 10,12, tvOS 10 e watchos 3.
* Aggiornamento alla build Master Clang più recente (2016-08-02)
* [Mantieni le opzioni di invio](https://twitter.com/Symbiatch/status/760373403878559744) di `sharpie pod bind` dati di telemetria da a. `sharpie bind`

## <a name="32-june-14-2016"></a>3,2 (14 giugno 2016)

[Scarica v 3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* Supporto per Xcode 8 Beta 1, iOS 10, macOS 10,12, tvOS 10 e watchos 3.

## <a name="31-may-31-2016"></a>3,1 (31 maggio 2016)

[Scarica v 3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* Supporto per CocoaPods 1,0
* Miglioramento dell'affidabilità del binding CocoaPods compilando `.framework` prima un oggetto nativo e quindi associando
* Copiare CocoaPods `.framework` e la definizione di associazione `Binding` in una directory per semplificare l'integrazione con i progetti di binding Novell. iOS e Novell. Mac

## <a name="30-october-5-2015"></a>3,0 (5 ottobre 2015)

[Scarica v 3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* Supporto per le nuove funzionalità del linguaggio Objective-C, inclusi i generics leggeri e i valori null, come introdotti in Xcode 7
* Supporto per gli SDK iOS e Mac più recenti.
* Supporto per la compilazione e l'analisi del progetto Xcode. A questo punto è possibile passare un progetto Xcode completo a Objective taglier, che consente di capire meglio la cosa da fare, ad esempio `sharpie bind Project.xcodeproj -sdk ios`.
* Supporto di [CocoaPods](https://cocoapods.org) . È ora possibile configurare, compilare e associare CocoaPods direttamente da Objective Sharpie (ad esempio `sharpie pod init ios AFNetworking && sharpie pod bind`).
* Quando si associano i Framework, i moduli Clang verranno abilitati se il Framework li supporta, causando l'analisi più corretta di un Framework, perché la struttura del Framework è definita `module.modulemap`da.
* Per i progetti Xcode che compilano un prodotto Framework, analizzare il prodotto anziché le destinazioni di prodotto intermedie come destinazioni non del Framework in un progetto Xcode può comunque avere ambiguità che non possono essere risolte automaticamente.

## <a name="216-march-17-2015"></a>2.1.6 (17 marzo 2015)

[Scarica v 2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* Associazione di espressioni operatore binario fisso: il lato sinistro dell'espressione è stato scambiato erroneamente con il lato destro, ad esempio `1 << 0` è stato associato erroneamente a. `0 << 1` Grazie a Adam Kemp, ho notato questo!
* Correzione di un problema `NSInteger` relativo `NSUInteger` a e `int` `uint` associatocome`nuint` e anziché eini386;`nint` viene ora passato a Clang per fare in modo `objc/NSObjCRuntime.h` che l'analisi funzioni come previsto in i386. `-DNS_BUILD_32_LIKE_64`
* L'architettura predefinita per Mac OS X SDK (ad esempio `-sdk macosx10.10`) è ora x86_64 anziché i386, pertanto `-arch` può essere omesso a meno che non si desideri eseguire l'override del valore predefinito.

## <a name="210-march-15-2015"></a>2.1.0 (15 marzo 2015)

[Scarica v 2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849): Assicurarsi `using ObjCRuntime;` che venga generato `ArgumentSemantic` quando si utilizza.
* [bxc#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850): Assicurarsi `using System.Runtime.InteropServices;` che venga generato `DllImport` quando si utilizza.
* [bxc#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852): Impostazione `DllImport` predefinita per il caricamento `__Internal`di simboli da.
* [bxc#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848): Ignorare le dichiarazioni del contenitore Objective-C dichiarate in avanti.
* [bxc#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846): Associare i tipi di protocollo con una singola qualificazione come`id<Foo>` interfacce `Foo` concrete `Foundation.NSObject<Foo>`(come anziché).
* [bxc#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037): Associare `UInt32`, `Int32` `u` `Int32`e valori letterali come per eliminare i suffissi e `uL` /o quando i valori possono essere inseriti in modo sicuro. `Int64` `UInt64`
* [bxc#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038): Correggere il mapping del nome enum quando il nome nativo originale `k` inizia con un prefisso.
* `sizeof`Le espressioni C il cui tipo di argomento non è C# mappato a un tipo primitivo verranno valutate in Clang e associate come valore letterale integer per evitare la generazione di valori non validi C#.
* Correzione della sintassi Objective-C per le proprietà il cui tipo è un blocco (il codice Objective-C viene visualizzato nei commenti sopra le dichiarazioni con binding).
* Associare i tipi decadenti come tipo originale`int[]` (decadimenti `int*` durante l'analisi semantica in Clang, ma associarlo come originale come scritto `int[]` ).

Grazie molto a Dave Dunkin per la segnalazione di molti bug corretti in questa versione.

## <a name="200-march-9-2015"></a>2.0.0: 9 marzo 2015

[Scarica v 2.0.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

Objective Sharpie 2,0 è una versione principale che include un driver e un parser basati su Clang migliorato e un nuovo motore di binding basato su NRefactory. Questi componenti migliorati forniscono associazioni molto migliori, in particolare per _quanto_ riguarda l'associazione di tipi. Sono stati apportati numerosi altri miglioramenti, che sono interni a Objective Sharpe che restituiranno molte funzionalità visibili all'utente nelle versioni future.

Objective Sharpie 2,0 si basa su 3.6.1 Clang.

### <a name="type-binding-improvements"></a>Miglioramenti dell'associazione di tipi

* Sono ora supportati i blocchi Objective-C. Sono inclusi i blocchi anonimi/inline e i `typedef`blocchi denominati tramite. I blocchi anonimi verranno associati `System.Action` come `System.Func` delegati o, mentre i blocchi denominati `delegate` verranno associati come tipi fortemente denominati.

* È stata migliorata l'euristica di denominazione per le enumerazioni anonime che sono immediatamente `typedef` precedute da una risoluzione in un tipo integrale incorporato `long` , `int`ad esempio o.

* I puntatori C sono ora associati C# `unsafe` come `System.IntPtr`puntatori anziché. In questo modo si ottiene una maggiore chiarezza nell'associazione per quando si desidera trasformare i parametri del `out` puntatore `ref` nei parametri o. Non è sempre possibile dedurre che un parametro debba essere `out` o `ref`, quindi il puntatore viene mantenuto nell'associazione per consentire un controllo più semplice.

* Un'eccezione all'associazione del puntatore precedente è quando viene rilevato un puntatore a 2 rango a un oggetto Objective-C come parametro. In questi casi, la convenzione è predominante e il parametro verrà associato come `out` ( `NSError **error` ad esempio → `out NSError error`).

### <a name="verify-attribute"></a>Verifica attributo

Si noterà spesso che i binding prodotti da Objective Sharpie verranno ora annotati con l' `[Verify]` attributo. Questi attributi indicano che è necessario _verificare_ che l'obiettivo Sharpie abbia fatto la corretta operazione confrontando l'associazione con la dichiarazione c/Objective-c originale (che verrà fornita in un commento sopra la dichiarazione associata).

La verifica  è consigliata per tutte le dichiarazioni associate, ma è probabilmente _necessaria_ per le dichiarazioni annotate `[Verify]` con l'attributo. Questo è dovuto al fatto che in molte situazioni i metadati non sono sufficienti nel codice sorgente nativo originale per dedurre il modo migliore per produrre un'associazione. Potrebbe essere necessario fare riferimento a documenti o commenti di codice all'interno dei file di intestazione per ottenere la migliore decisione di associazione.

Per ulteriori informazioni, vedere la documentazione relativa alla [Verifica degli attributi](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

### <a name="other-notable-improvements"></a>Altri miglioramenti importanti

* `using`le istruzioni vengono ora generate in base ai tipi associati. Se ad esempio è stato `NSURL` associato un oggetto, `using Foundation;` verrà generata anche un'istruzione.

* `struct`le `union` dichiarazioni e verranno ora associati, usando il trucco `[FieldOffset]` per le unioni.

* I valori enum con inizializzatori di espressione costanti verranno ora associati correttamente; l'espressione completa viene convertita C#in.

* I blocchi e i metodi Variadic sono ora associati.

* I Framework sono ora supportati tramite l' `-framework` opzione. Per altri dettagli, vedere la documentazione sull' [associazione di framework nativi](~/cross-platform/macios/binding/objective-sharpie/index.md) .

* Il codice sorgente Objective-C verrà automaticamente rilevato, che dovrebbe eliminare la necessità di passare `-ObjC` manualmente o `-xobjective-c` Clang.

* Il modulo Clang Usage`@import`() viene ora rilevato automaticamente, che dovrebbe eliminare la necessità di passare `-fmodules` manualmente a Clang per le librerie che usano il supporto del nuovo modulo in Clang.

* Il API unificata Novell è ora la destinazione di associazione predefinita; usare l' `-classic` opzione per la destinazione solo a 32 bit API classica.

### <a name="notable-bug-fixes"></a>Correzioni di bug rilevanti

* Correzione `instancetype` dell'associazione quando viene usata in una categoria Objective-C
* Nome completo delle categorie Objective-C
* Prefisso i `I` `INSCopying` protocolliObjective-Ccon(ad`NSCopying`esempio, anziché)

## <a name="1135-december-21-2014"></a>1.1.35: 21 dicembre 2014

[Scarica v 1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

Correzioni di bug secondari.

## <a name="111-december-15-2014"></a>1.1.1: 15 dicembre 2014

[Scarica v 1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 è stata la prima versione principale dopo 1,5 anni di utilizzo e sviluppo interni in Novell dopo l'anteprima iniziale di Objective Sharpie del 2013 aprile. Questa versione è la prima ad essere considerata stabile e utilizzabile per un'ampia gamma di librerie native, con un nuovo back-end Clang.

