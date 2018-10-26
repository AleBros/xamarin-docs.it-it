---
title: Supporto di Objective-C
description: Questo documento fornisce una descrizione del supporto per Objective-C in .NET di incorporamento. Illustra il conteggio dei riferimenti automatico, NSString, protocolli, NSObject protocollo, eccezioni e altro ancora.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 48caa70cf2bd408f8afc673b400f7d5a4369e108
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110726"
---
# <a name="objective-c-support"></a>Supporto di Objective-C

## <a name="specific-features"></a>Funzionalità specifiche

La generazione di Objective-C offre alcune funzionalità speciali che è importante sottolineare.

### <a name="automatic-reference-counting"></a>Il conteggio dei riferimenti automatico

L'uso di automatica riferimento il conteggio (arco) è **obbligatorio** per chiamare le associazioni generate. Progetto usa una libreria basata su .NET incorporamento deve essere compilato con `-fobjc-arc`.

### <a name="nsstring-support"></a>Supporto NSString

Le API che espongono `System.String` i tipi vengono convertiti in `NSString`. Questo semplifica notevolmente la gestione della memoria quando si gestiscono `char*`.

### <a name="protocols-support"></a>Supporto di protocolli

Interfacce gestite vengono convertite in protocolli di Objective-C in cui tutti i membri sono `@required`.

### <a name="nsobject-protocol-support"></a>Supporto del protocollo NSObject

Per impostazione predefinita, l'hash predefinito e verificarne l'uguaglianza di .NET e il runtime di Objective-C si presuppone che sia intercambiabile, mentre condividono una semantica simile.

Quando un tipo gestito esegue l'override `Equals(Object)` o `GetHashCode`, in genere significa che il comportamento predefinito (.NET) non era sufficiente; ciò implica che il comportamento di Objective-C predefinito è probabile che non è sufficiente uno.

In questi casi, il generatore esegue l'override di [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) (metodo) e [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) definita nella proprietà il [ `NSObject` protocollo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). In questo modo l'implementazione gestita personalizzata da utilizzare dal codice Objective-C in modo trasparente.

### <a name="exceptions-support"></a>Supporto eccezioni

Il passaggio `--nativeexception` come argomento a `objcgen` convertirà le eccezioni gestite in eccezioni Objective-C che possono essere rilevate ed elaborate. 

### <a name="comparison"></a>Confronto

Gestito i tipi che implementano `IComparable` (o la versione generica `IComparable<T>`) genera metodi semplici di Objective-C che restituiscono una `NSComparisonResult` e accettare un `nil` argomento. In questo modo più intuitivo per gli sviluppatori Objective-C API generata. Ad esempio:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categories

Estensioni metodi vengono convertiti nelle categorie gestite. Ad esempio, i seguenti metodi di estensione su `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

creare una categoria di Objective-C come questo:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Quando un singolo tipo gestito estende tipi diversi, vengono generate più categorie di Objective-C.

### <a name="subscripting"></a>Indice

Proprietà indicizzate gestite vengono convertite nell'indice di oggetto. Ad esempio:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

creare Objective-C simile a:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

che può essere usato tramite la sintassi di indice di Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

A seconda del tipo dell'indicizzatore, l'indice indicizzato o con chiave verrà generato nei casi appropriati.

Ciò [articolo](http://nshipster.com/object-subscripting/) è un'ottima introduzione per l'indice.

## <a name="main-differences-with-net"></a>Differenze principali con .NET

### <a name="constructors-vs-initializers"></a>Inizializzatori di costruttori Visual Studio

In Objective-C, è possibile chiamare tutte dell'inizializzatore di prototipi di qualsiasi delle classi padre nella catena di ereditarietà, a meno che non è contrassegnato come non disponibile (`NS_UNAVAILABLE`).

In C# è necessario dichiarare in modo esplicito un membro costruttore all'interno di una classe, ovvero i costruttori non vengono ereditati.

Per esporre la rappresentazione a destra del C# API Objective-c `NS_UNAVAILABLE` viene aggiunto a qualsiasi inizializzatore che non è presente nella classe figlio dalla classe padre.

C#API:

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

Objective-C rilevate API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

In questo caso, `initWithId:` è stato contrassegnato come non disponibile.

### <a name="operator"></a>Operatore

Objective-C non supporta l'operatore di overload come C# esegue, in modo che gli operatori vengono convertiti in selettori di classe:

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

in

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Tuttavia, alcuni linguaggi .NET non supportano overload degli operatori, pertanto è comune per includere anche un ["descrittivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) denominato metodo oltre l'overload dell'operatore.

Se la versione di operatore e la versione "per" vengono trovati, verrà generata solo la versione descrittiva, come i generatori produrranno per lo stesso nome di Objective-C.

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

diventa:

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operatore di uguaglianza

In generale operator `==` in C# viene gestita come un operatore generale come indicato sopra.

Tuttavia, se l'operatore di uguaglianza "descrittivo" viene trovato, entrambi operator `==` e l'operatore `!=` verranno ignorati durante la generazione.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

Dal [ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentazione:

> `NSDate` gli oggetti incapsulano un singolo punto nel tempo, indipendente da qualsiasi sistema calendrical particolare o di un fuso orario. Gli oggetti di data non sono modificabili, che rappresenta un intervallo di tempo invariante rispetto alla data di riferimento assoluto (00:00:1 gennaio 2001 00 UTC).

A causa dell'errore `NSDate` fanno riferimento a data, tutte le conversioni tra questo e `DateTime` deve essere eseguita in formato UTC.

#### <a name="datetime-to-nsdate"></a>Data/ora per NSDate

Durante la conversione da `DateTime` al `NSDate`, il `Kind` proprietà `DateTime` viene presa in considerazione:

|Kind|Risultati|
|---|---|
|`Utc`|La conversione viene eseguita utilizzando l'oggetto fornito `DateTime` come è.|
|`Local`|Il risultato della chiamata al metodo `ToUniversalTime()` nell'oggetto fornito `DateTime` oggetto viene usato per la conversione.|
|`Unspecified`|L'oggetto fornito `DateTime` oggetto presuppone che sia UTC, pertanto, in modo analogo durante `Kind` è `Utc`.|

La conversione Usa la formula seguente:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

In questa formula: 

- `NSDateReferenceDateTicks` viene calcolato sulla base di `NSDate` fanno riferimento a data di 00:00:00 UTC del 1 gennaio 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) è definito in [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Per creare il `NSDate` oggetti, il `TimeInterval` viene usato con la `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) selettore.

#### <a name="nsdate-to-datetime"></a>NSDate in DateTime

La conversione da `NSDate` a `DateTime` Usa la formula seguente:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

In questa formula: 

- `NSDateReferenceDateTicks` viene calcolato sulla base di `NSDate` fanno riferimento a data di 00:00:00 UTC del 1 gennaio 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) è definito in [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Al termine del calcolo `DateTimeTicks`, il `DateTime` [costruttore](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) viene richiamato, l'impostazione relativa `kind` a `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` può essere `nil`, ma un `DateTime` è uno struct in .NET, che, per definizione, non può essere `null`. Qualora il Licenziatario fornisca un `nil` `NSDate`, per impostazione predefinita verrà tradotto `DateTime` valore, che esegue il mapping a `DateTime.MinValue`.

`NSDate` supporta un massimo più alto e un valore minimo inferiore a `DateTime`. Durante la conversione da `NSDate` per `DateTime`, questi valori superiori e inferiori vengono modificati per il `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) oppure [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue)rispettivamente.
