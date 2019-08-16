---
title: Limitazioni di Novell. iOS
description: Questo documento descrive le limitazioni di Novell. iOS, la discussione di generics, le sottoclassi generiche di NSObjects, P/Invoke negli oggetti generici e altro ancora.
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/09/2018
ms.openlocfilehash: f3baa6756b4e72b750ff1b128b72802f8f1ce6d9
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527344"
---
# <a name="limitations-of-xamarinios"></a>Limitazioni di Novell. iOS

Poiché le applicazioni che usano Novell. iOS vengono compilate in codice statico, non è possibile usare tutte le funzionalità che richiedono la generazione di codice in fase di esecuzione.

Queste sono le limitazioni di Novell. iOS rispetto al desktop mono:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>Supporto per generics limitato

A differenza di mono/.NET tradizionale, il codice sull'iPhone viene compilato in anticipo in modo statico anziché essere compilato su richiesta da un compilatore JIT.

La tecnologia [AOT completa](https://www.mono-project.com/docs/advanced/aot/#full-aot) di mono presenta alcune limitazioni rispetto ai generics, perché non tutte le possibili creazioni di istanze generiche possono essere determinate in anticipo in fase di compilazione. Questo non è un problema per i Runtime .NET o mono normali perché il codice viene sempre compilato in fase di esecuzione usando il compilatore just-in-time. Questa operazione costituisce tuttavia una sfida per un compilatore statico come Novell. iOS.

Di seguito sono riportati alcuni dei problemi comuni eseguiti dagli sviluppatori:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Le sottoclassi generiche di NSObjects sono limitate

Novell. iOS dispone attualmente di un supporto limitato per la creazione di sottoclassi generiche della classe NSObject, ad esempio nessun supporto per i metodi generici. A partire da 7.2.1, è possibile usare le sottoclassi generiche di NSObjects, come questa:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Sebbene le sottoclassi generiche di NSObjects siano possibili, esistono alcune limitazioni. Per ulteriori informazioni, leggere le [sottoclassi generiche del documento NSObject](~/ios/internals/api-design/nsobject-generics.md)


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>Nessuna generazione di codice dinamico

Poiché il kernel iOS impedisce a un'applicazione di generare in modo dinamico il codice, Novell. iOS non supporta alcuna forma di generazione del codice dinamico. Sono inclusi:

- System. Reflection. Emit non è disponibile.
- Nessun supporto per System. Runtime. Remoting.
- Nessun supporto per la creazione dinamica dei tipi (nessun tipo. GetType ("MyType" 1 ")), sebbene la ricerca dei tipi esistenti (Type. GetType (" System. String "), ad esempio, funzioni correttamente. 
- I callback inversi devono essere registrati con il runtime in fase di compilazione.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

La mancanza di System. Reflection. **Emit** significa che il codice che dipende dalla generazione del codice di runtime funzionerà. Sono inclusi elementi come:

- Dynamic Language Runtime.
- Qualsiasi linguaggio basato su Dynamic Language Runtime.
- TransparentProxy di comunicazione remota o qualsiasi altro elemento che comporterebbe la generazione dinamica del codice da parte del runtime. 


  > [!IMPORTANT]
  > Non confondere **Reflection. Emit** con **Reflection**. Reflection. Emit riguarda la generazione dinamica del codice e la compilazione del codice JIT e la compilazione in codice nativo. A causa delle limitazioni di iOS (nessuna compilazione JIT), questo non è supportato.

Tuttavia, l'intera API di reflection, incluso Type. GetType ("someClass"), elencare i metodi, elencare le proprietà, recuperare gli attributi e i valori funziona correttamente.

### <a name="using-delegates-to-call-native-functions"></a>Uso di delegati per chiamare funzioni native

Per chiamare una funzione nativa tramite un C# delegato, la dichiarazione del delegato deve essere decorata con uno degli attributi seguenti:

- [UnmanagedFunctionPointerAttribute](xref:System.Runtime.InteropServices.UnmanagedFunctionPointerAttribute) (scelta consigliata, poiché è multipiattaforma e compatibile con .NET Standard 1.1 +)
- [MonoNativeFunctionWrapperAttribute](xref:ObjCRuntime.MonoNativeFunctionWrapperAttribute)

Se non si specifica uno di questi attributi, verrà generato un errore di runtime, ad esempio:

```
System.ExecutionEngineException: Attempting to JIT compile method '(wrapper managed-to-native) YourClass/YourDelegate:wrapper_aot_native(object,intptr,intptr)' while running in aot-only mode.
```
 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Callback inversi

In mono standard è possibile passare C# le istanze delegate al codice non gestito invece di un puntatore a funzione. Il runtime trasforma in genere i puntatori a funzione in un piccolo thunk che consente al codice non gestito di richiamare il codice gestito.

In mono questi Bridge vengono implementati dal compilatore just-in-time. Quando si usa il compilatore ahead of time richiesto dall'iPhone, a questo punto sono presenti due importanti limitazioni:

- È necessario contrassegnare tutti i metodi di callback con [MonoPInvokeCallbackAttribute](xref:ObjCRuntime.MonoPInvokeCallbackAttribute)
- I metodi devono essere metodi statici e non è disponibile alcun supporto per i metodi di istanza.
 
<a name="No_Remoting" />

## <a name="no-remoting"></a>Nessuna comunicazione remota

Lo stack di comunicazione remota non è disponibile in Novell. iOS.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>Funzionalità disabilitate in fase di esecuzione

Le funzionalità seguenti sono state disabilitate nel runtime iOS di mono:

- Profiler
- Reflection. Emit
- Funzionalità Reflection. Emit. Save
- Associazioni COM
- Motore JIT
- Verifier dei metadati (poiché non esiste alcun JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Limitazioni dell'API .NET

L'API .NET esposta è un subset del Framework completo, in quanto non tutti gli elementi sono disponibili in iOS. Per un [elenco degli assembly attualmente supportati](~/cross-platform/internals/available-assemblies.md), vedere le domande frequenti.



In particolare, il profilo API usato da Novell. iOS non include System. Configuration, pertanto non è possibile usare file XML esterni per configurare il comportamento del runtime.
