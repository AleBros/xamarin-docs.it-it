---
title: Limitazioni
ms.topic: article
ms.prod: xamarin
ms.assetid: 5AC28F21-4567-278C-7F63-9C2142C6E06A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 43b099e8ddd6acc3e8cc4ce94580313a39a0c686
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="limitations"></a>Limitazioni

Poiché le applicazioni su iPhone usando xamarin. IOS vengono compilate in codice statico, non è possibile utilizzare qualsiasi funzionalità che richiedono la generazione di codice in fase di esecuzione.

Queste sono le limitazioni di xamarin rispetto al desktop Mono:

 <a name="Limited_Generics_Support" />


## <a name="limited-generics-support"></a>Supporto limitato dei Generics

A differenza dei tradizionali Mono/.NET, codice su iPhone viene compilato in modo statico anticipatamente anziché in fase di compilazione su richiesta da un compilatore JIT.

Del mono [AOT completo](http://www.mono-project.com/AOT#Full_AOT) tecnologia presenta alcune limitazioni per quanto riguarda i generics, questi errori sono causati poiché non tutte le possibili istanza generica può essere determinato in anticipo in fase di compilazione. Questo non è un problema per il runtime .NET o Mono regolari come il codice viene compilato sempre in fase di esecuzione utilizzando il solo nel compilatore Time. Ma ciò pone un problema per un compilatore statico come xamarin. IOS.

Alcuni dei problemi comuni che gli sviluppatori di eseguire in includono:

 <a name="Generic_Subclasses_of_NSObjects_are_limited" />


### <a name="generic-subclasses-of-nsobjects-are-limited"></a>Le sottoclassi di NSObjects generico sono limitate

Xamarin. IOS attualmente offre supporto limitato per la creazione di sottoclassi generiche della classe NSObject, ad esempio Nessun supporto per i metodi generici. A partire da 7.2.1, utilizzando le sottoclassi generiche di NSObjects è possibile, simile alla seguente:

```csharp
class Foo<T> : UIView {
    [..]
}
```

> [!NOTE]
> Mentre le sottoclassi generiche di NSObjects sono possibili, esistono alcune limitazioni. Lettura di [generiche sottoclassi di NSObject](~/ios/internals/api-design/nsobject-generics.md) per ulteriori informazioni



### <a name="pinvokes-in-generic-types"></a>P/Invoke in tipi generici

P/Invoke in classi generiche non sono supportate:

```csharp
class GenericType<T> {
    [DllImport ("System")]
    public static extern int getpid ();
}
```

 <a name="Property.SetInfo_on_a_Nullable_Type_is_not_supported" />


### <a name="propertysetinfo-on-a-nullable-type-is-not-supported"></a>Property.SetInfo in un tipo Nullable non è supportata.

Utilizzo Property.SetInfo della Reflection per impostare il valore su un tipo Nullable&lt;T&gt; non è attualmente supportato.

 <a name="Value_types_as_Dictionary_Keys" />


### <a name="value-types-as-dictionary-keys"></a>Tipi di valore come chiavi del dizionario

Utilizzo di un tipo di valore come un dizionario&lt;TKey, TValue&gt; chiave risulta problematica, come il valore predefinito costruttore dizionario tenta di utilizzare EqualityComparer&lt;TKey&gt;. Per impostazione predefinita. EqualityComparer&lt;TKey&gt;. Impostazione predefinita, a sua volta, tenta di usare la Reflection per creare un'istanza di un nuovo tipo che implementa l'oggetto IEqualityComparer&lt;TKey&gt; interfaccia.

Questo procedimento funziona per i tipi di riferimento (come la reflection + creare un nuovo passaggio di tipo viene ignorato), ma in caso di valore tipi di arresti anomali del sistema e si esegue il burn-piuttosto rapidamente una volta che si tenta di utilizzarla nel dispositivo.

 **Soluzione alternativa**: implementare manualmente il [IEqualityComparer&lt;TKey&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/) in un nuovo tipo di interfaccia e fornire un'istanza di tale tipo di [dizionario&lt;TKey, TValue&gt; ](https://developer.xamarin.com/api/type/System.Collections.Generic.Dictionary%3CTKey,TValue%3E/) [(IEqualityComparer&lt;TKey&gt;)](https://developer.xamarin.com/api/type/System.Collections.Generic.IEqualityComparer%601/) costruttore.


 <a name="No_Dynamic_Code_Generation" />


## <a name="no-dynamic-code-generation"></a>Nessuna generazione di codice dinamico

Poiché il kernel dell'iPhone impedisce la generazione di codice in modo dinamico alle applicazioni Mono su iPhone non supporta alcuna forma di generazione di codice dinamico. Sono inclusi:

-  Il System.Reflection.Emit non è disponibile.
-  Nessun supporto per System.Runtime.Remoting.
-  Nessun supporto per la creazione di tipi in modo dinamico (nessun Type. GetType ("MyType" 1")), anche se la ricerca di tipi esistenti (Type. GetType ("System. String"), ad esempio, funziona correttamente). 
-  I callback inversi devono essere registrati con il runtime in fase di compilazione.


 
 <a name="System.Reflection.Emit" />


### <a name="systemreflectionemit"></a>System.Reflection.Emit

La mancanza di System. Reflection. **Generare** indica che nessun codice che dipende dalla generazione del codice runtime funzionerà. Tra cui:

-  Dynamic Language Runtime.
-  Tutte le lingue basate su Dynamic Language Runtime.
-  TransparentProxy dei servizi remoti o qualsiasi altro elemento che fa sì che il runtime generare il codice in modo dinamico. 


 **Importante:** non confondere **Emit** con **Reflection**. Reflection. Emit sulla generazione di codice in modo dinamico e che hanno il codice JIT e il codice compilato in codice nativo. A causa delle limitazioni su iPhone (Nessuna compilazione JIT) questo non è supportato.

Tuttavia, l'intera API di Reflection, tra cui Type. GetType ("someClass"), elenco di metodi, elenco delle proprietà, il recupero di attributi e valori funziona correttamente.

 
 <a name="Reverse_Callbacks" />


### <a name="reverse-callbacks"></a>Invertire i callback

In Mono standard è possibile passare istanze dei delegati c# al codice non gestito anziché un puntatore a funzione. Il runtime in genere Trasforma i puntatori a funzione in un thunk di piccole dimensioni che consente a codice non gestito richiamare il codice gestito.

In Mono questi Bridge vengono implementati da Just-in-Time compilatore. Quando tramite il compilatore ahead di tempo richiesto da iPhone esistono due importanti limitazioni a questo punto:

-  È necessario contrassegnare tutti i metodi di callback con il [MonoPInvokeCallbackAttribute](https://developer.xamarin.com/api/type/MonoPInvokeCallbackAttribute/) 
-  I metodi devono essere metodi statici, è disponibile alcun supporto per l'istanza metodi. 


 
 <a name="No_Remoting" />


## <a name="no-remoting"></a>Nessuna comunicazione remota

Lo stack di comunicazione remota non è disponibile in xamarin. IOS.


 <a name="Runtime_Disabled_Features" />


## <a name="runtime-disabled-features"></a>Runtime disabilitato funzionalità

Le funzionalità seguenti sono state disabilitate in iOS del Mono Runtime:

-  Profiler
-  Reflection. Emit
-  Funzionalità Reflection.Emit.Save
-  Associazioni di COM
-  Il motore JIT
-  I metadati di sistema di verifica (poiché non esiste alcun JIT)


 <a name=".NET_API_Limitations" />


## <a name="net-api-limitations"></a>Limitazioni dell'API .NET

L'API .NET esposti è un subset del framework completo non è sempre disponibile in iOS. Vedere le domande frequenti per un [elenco degli assembly attualmente supportati](~/cross-platform/internals/available-assemblies.md).



In particolare, il profilo di API usato da xamarin non includono System. Configuration, pertanto non è possibile utilizzare file XML esterni per configurare il comportamento di runtime.
