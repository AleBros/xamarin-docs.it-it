---
title: Configurazione personalizzata del linker
ms.topic: article
ms.prod: xamarin
ms.assetid: F8A99E3F-2197-4399-AC81-F1DBAB5729C9
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: d5470dfc314677dbe558bca2f3ddfa107354c752
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="custom-linker-configuration"></a>Configurazione personalizzata del linker

Se il set predefinito di opzioni non è sufficiente, è possibile gestire il processo di collegamento con un file XML che descrive la configurazione desiderata per il linker.

È possibile fornire definizioni aggiuntive al linker per impedire che il tipo, determinati metodi e/o determinati campi vengano eliminati dall'applicazione. Nel codice l'approccio consigliato consiste nell'usare l'attributo personalizzato `[Preserve]`, come descritto nelle guide [Collegamento in iOS](~/ios/deploy-test/linker.md) e [Collegamento in Android](~/android/deploy-test/linker.md).
Tuttavia, se sono necessarie definizioni dagli assembly SDK o di prodotto, l'uso di un file XML può essere la soluzione migliore, rispetto all'aggiunta di codice che impedisca al linker di eliminare elementi necessari.

A questo scopo, definire un file XML con l'elemento di primo livello <linker> che contiene nodi *assembly*, che a loro volta contengono nodi *type*, che contengono infine nodi *method* e *field*.

Quando questo file di descrizione del linker è disponibile, aggiungerlo al progetto e quindi:

-  **Per Android**: impostare **Azione di compilazione** su **LinkDescription**
-  **Per iOS**: impostare **Azione di compilazione** su **LinkDescription**


L'esempio seguente mostra che aspetto avrà il file XML:

```xml
<linker>
        <assembly fullname="mscorlib">
                <type fullname="System.Environment">
                        <field name="mono_corlib_version" />
                        <method name="get_StackTrace" />
                </type>
        </assembly>
        <assembly fullname="My.Own.Assembly">
                <type fullname="Foo" preserve="fields">
                        <method name=".ctor" />
                </type>
                <type fullname="Bar">
                        <method signature="System.Void .ctor(System.String)" />
                        <field signature="System.String _blah" />
                </type>
                <namespace fullname="My.Own.Namespace" />
                <type fullname="My.Other*" />
        </assembly>
</linker>
```

Nell'esempio precedente il linker leggerà e applicherà le istruzioni negli assembly `mscorlib.dll` (forniti con Mono per Android) e `My.Own.Assembly` (codice utente).

La prima sezione, per `mscorlib.dll`, fa sì che il tipo `System.Environment` mantenga il campo denominato `mono_corlib_version` e il metodo `get_StackTrace`.
Si noti che è necessario usare i nomi dei metodi getter e/o setter, perché il linker funziona con IL e non riconosce le proprietà C#.

La seconda sezione, per `My.Own.Assembly.dll`, fa sì che il tipo `Foo` mantenga tutti i campi, ovvero l'attributo `preserve="fields"`, e tutti i costruttori, ovvero tutti i metodi denominati `.ctor` in IL. Il tipo `Bar` manterrà firme specifiche (non i nomi) per un costruttore (che accetta un solo parametro stringa) e per un campo stringa `_blah` specifico.
Lo spazio dei nomi `My.Own.Namespace` manterrà tutti i tipi che contiene.
Infine, qualsiasi tipo il cui nome completo (incluso lo spazio dei nomi) corrisponde al modello con carattere jolly "My.Other\*" manterrà tutti i campi e i metodi. Il carattere jolly `*` può essere incluso più volte all'interno di un modello "type fullname".



## <a name="related-links"></a>Collegamenti correlati

- [Collegamento in iOS](~/ios/deploy-test/linker.md)
- [Collegamento in Android](~/android/deploy-test/linker.md)
