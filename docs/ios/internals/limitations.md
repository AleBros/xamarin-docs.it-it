---
title: Limitazioni di xamarin. IOS
description: Questo documento descrive le limitazioni di xamarin. IOS, discutere dei generics, sottoclassi generiche di NSObjects, P/Invoke in oggetti generici e altro ancora.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/09/2018
ms.openlocfilehash: b79d3683c8e4979cbbd13550f3df86c39622ad2b
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870179"
---
# <a name="limitations-of-xamarinios"></a>Limitazioni di xamarin. IOS

Poiché applicazioni mediante xamarin. IOS vengono compilate in codice statico, non è possibile usare qualsiasi funzionalità che richiedono la generazione di codice in fase di esecuzione.

Queste sono le limitazioni di xamarin. IOS rispetto al desktop Mono:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>Supporto limitato dei Generics

A differenza di Mono/.NET tradizionali, codice su iPhone è compilato in modo statico anticipo anziché in fase di compilazione su richiesta tramite un compilatore JIT.

Mono [AOT completo](https://www.mono-project.com/docs/advanced/aot/#full-aot) tecnologia presenta alcune limitazioni per quanto riguarda i generics, questi errori sono causati poiché non tutte le possibili istanza generica può essere determinato fin dall'inizio in fase di compilazione. Ciò non è un problema per i Runtime .NET o Mono regolari perché il codice viene sempre compilato in fase di esecuzione usando il Just nel compilatore ora. Ma ciò costituisce una sfida per un compilatore statico, ad esempio xamarin. IOS.

Alcuni dei problemi comuni che gli sviluppatori verificarsi, includono:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Sottoclassi generiche di NSObjects sono limitate

Xamarin. IOS attualmente include supporto limitato per la creazione di sottoclassi generiche della classe NSObject, ad esempio Nessun supporto per metodi generici. A partire da 7.2.1, utilizzando le sottoclassi generiche di NSObjects è possibile, simile alla seguente:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Anche se sono possibili sottoclassi generiche di NSObjects, esistono alcune limitazioni. Leggere il [sottoclassi generiche di NSObject](~/ios/internals/api-design/nsobject-generics.md) documento per altre informazioni


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>Nessuna generazione di codice dinamico

Poiché il kernel iOS impedisce la generazione di codice in modo dinamico di un'applicazione, xamarin. IOS non supporta alcuna forma di generazione dinamica del codice. Sono inclusi:

-  Il System.Reflection.Emit non è disponibile.
-  Nessun supporto per Remoting.
-  Nessun supporto per la creazione dinamica di tipi (nessun Type. GetType ("MyType" 1")), anche se la ricerca di tipi esistenti (Type. GetType ("System. String"), ad esempio, funziona correttamente). 
-  I callback inversi devono essere registrati con il runtime in fase di compilazione.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

La mancanza di System. Reflection. **Generare** indica che nessun codice che dipende dalla generazione del codice runtime funzionerà. Sono incluse operazioni come:

-  Dynamic Language Runtime.
-  Tutte le lingue basate su Dynamic Language Runtime.
-  TransparentProxy dei servizi remoti o qualsiasi altra cosa che provocherebbe la fase di esecuzione generare il codice in modo dinamico. 


 **Importante:** Non confondere **Reflection. Emit** con **Reflection**. Reflection. Emit sulla generazione di codice in modo dinamico e che hanno tale codice JITed e compilate nel codice nativo. A causa di limitazioni in iOS (Nessuna compilazione JIT) questo non è supportato.

Tuttavia, l'intera API di Reflection, tra cui Type. GetType ("someClass"), elenco di metodi, elenco delle proprietà, il recupero di attributi e valori funziona alla perfezione.

### <a name="using-delegates-to-call-native-functions"></a>Uso dei delegati per chiamare le funzioni Native

Per chiamare una funzione nativa tramite un delegato c#, la dichiarazione del delegato deve essere decorata con uno dei seguenti attributi:

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (scelta consigliata, poiché è multipiattaforma e compatibile con .NET Standard 1.1 +)
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

Incapacità di fornire uno di questi attributi genererà un errore di runtime, ad esempio:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Callback inversi

In Mono standard è possibile passare istanze dei delegati c# al codice non gestito al posto di un puntatore a funzione. Il runtime trasforma in genere questi puntatori a funzione in un thunk di piccole dimensioni che consente al codice non gestito richiamare il codice gestito.

In Mono questi adattatori vengono implementati da Just-in-Time compilatore. Quando tramite il compilatore ahead of time richiesto da iPhone esistono due importanti limitazioni a questo punto:

-  È necessario contrassegnare tutti i metodi di callback con la [MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)
-  I metodi devono essere metodi statici, non è supportato, ad esempio i metodi.
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>Nessuna comunicazione remota

Lo stack di comunicazione remota non è disponibile in xamarin. IOS.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>Runtime disabilitato funzionalità

Le funzionalità seguenti sono state disabilitate in iOS di Mono Runtime:

-  Profiler
-  Reflection.Emit
-  Funzionalità Reflection.Emit.Save
-  Associazioni di COM
-  Il motore JIT
-  I metadati di sistema di verifica (poiché non esiste alcun JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Limitazioni dell'API .NET

L'API .NET esposta è un subset del framework completo perché non tutti gli elementi disponibile in iOS. Vedi le domande frequenti per un [elenco di assembly attualmente supportati](~/cross-platform/internals/available-assemblies.md).



In particolare, il profilo di API usato da xamarin. IOS non includono System. Configuration, pertanto non è possibile usare i file XML esterni per configurare il comportamento di runtime.
