---
title: Supporto di Objective-C
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 1c0d4ac79bba0846b62c4ca74c42cd86610caa1b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="objective-c-support"></a>Supporto di Objective-C

## <a name="specific-features"></a>Funzionalità specifiche

La generazione di Objective-C dispone di alcune funzionalità speciali che vengono non degni di nota.

### <a name="automatic-reference-counting"></a>Conteggio dei riferimenti automatico

L'uso di automatico riferimento conteggio ARC () è **obbligatorio** per chiamare le associazioni generate. Utilizzo di una raccolta basata su .NET incorporamento progetto deve essere compilato con `-fobjc-arc`.

### <a name="nsstring-support"></a>Supporto NSString

Le API che espongono `System.String` tipi vengono convertiti in `NSString`. Tale procedura facilita la gestione della memoria rispetto a quando si gestiscono `char*`.

### <a name="protocols-support"></a>Supporto di protocolli

Interfacce gestite vengono convertite in protocolli Objective-C in cui tutti i membri sono `@required`.

### <a name="nsobject-protocol-support"></a>Supporto del protocollo NSObject

Per impostazione predefinita, l'hashing predefinito e uguaglianza di .NET e il runtime Objective-C si presuppone che per essere intercambiabili, in quanto condividono una semantica simile.

Quando un tipo gestito esegue l'override `Equals(Object)` o `GetHashCode`, in genere significa che il comportamento predefinito (.NET) non era sufficiente; ciò implica che il comportamento di Objective-C predefinito è probabile che non sufficienti entrambi.

In tali casi, il generatore esegue l'override di [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) (metodo) e [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) definita nella proprietà il [ `NSObject` protocollo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). In questo modo l'implementazione gestita personalizzata da utilizzare in modo trasparente dal codice Objective-C.

### <a name="exceptions-support"></a>Supporto eccezioni

Passando `--nativeexception` come argomento a `objcgen` convertirà le eccezioni gestite in Objective-C eccezioni che possono essere rilevate ed elaborate. 

### <a name="comparison"></a>Confronto

Gestito i tipi che implementano `IComparable` (o la versione generica `IComparable<T>`) produrrà Objective-C descrittivi metodi che restituiscono un `NSComparisonResult` e accettare un `nil` argomento. In questo modo più semplice per gli sviluppatori Objective-C API generata. Ad esempio:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categories

Estensioni di metodi vengono convertiti in categorie gestito. Ad esempio, i seguenti metodi di estensione su `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

creare una categoria di Objective-C come questa:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Quando un singolo tipo gestito estende tipi diversi, vengono generate più categorie Objective-C.

### <a name="subscripting"></a>Indice

Proprietà indicizzate gestite vengono convertite nell'indice di oggetto. Ad esempio:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

creerebbero Objective-C simile a:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

che può essere usato tramite la sintassi di indice Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

A seconda del tipo dell'indicizzatore, verrà generato l'indice di chiave o indicizzata dove appropriato.

Questo [articolo](http://nshipster.com/object-subscripting/) rappresenta un ottimo inizio per l'indice.

## <a name="main-differences-with-net"></a>Differenze principali con .NET

### <a name="constructors-vs-initializers"></a>Inizializzatori di costruttori vs

In Objective-C, è possibile chiamare qualsiasi dell'inizializzatore di prototipi di una qualsiasi delle classi padre nella catena di ereditarietà, a meno che non è contrassegnato come non disponibile (`NS_UNAVAILABLE`).

In c# è necessario dichiarare in modo esplicito un membro di costruttore all'interno di una classe, ovvero i costruttori non vengono ereditati.

Per esporre la rappresentazione a destra dell'API c# per Objective-C, `NS_UNAVAILABLE` viene aggiunto a qualsiasi inizializzatore non è presente nella classe figlio dalla classe padre.

API DI C#:

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

Objective-C non supporta l'operatore di overload come c#, in modo che gli operatori vengono convertiti in selettori di classe:

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

Tuttavia, alcuni linguaggi .NET non supportano overload degli operatori, pertanto è comune includere inoltre un ["descrittivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) denominato metodo oltre l'overload dell'operatore.

Se sia la versione di operatore e la versione "descrittivo" vengono trovati, verrà generata solo la versione descrittiva, come i generatori produrranno per lo stesso nome di Objective-C.

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

Diventa:

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operatore di uguaglianza

In generale operatore `==` in c# viene gestita in genere operatore come indicato in precedenza.

Tuttavia, se l'operatore Equals "descrittivo" non viene trovata, entrambi operatore `==` e l'operatore `!=` verranno ignorati durante la generazione.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

Dal [ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentazione:

> `NSDate` oggetti incapsulano un singolo punto nel tempo, indipendente da qualsiasi sistema calendrical particolare o di un fuso orario. Gli oggetti di data non sono modificabili, che rappresenta un intervallo di tempo invariante rispetto alla data di un riferimento assoluto (00:00:1 gennaio 2001 00 UTC).

A causa di `NSDate` fanno riferimento a data, tutte le conversioni tra questo e `DateTime` deve essere eseguita in formato UTC.

#### <a name="datetime-to-nsdate"></a>DateTime per NSDate

Durante la conversione da `DateTime` al `NSDate`, la `Kind` proprietà `DateTime` viene presa in considerazione:

|Kind|Risultati|
|---|---|
|`Utc`|La conversione viene eseguita utilizzando l'oggetto fornito `DateTime` dell'oggetto come.|
|`Local`|Il risultato della chiamata al metodo `ToUniversalTime()` nell'oggetto fornito `DateTime` oggetto viene utilizzato per la conversione.|
|`Unspecified`|L'oggetto fornito `DateTime` oggetto presuppone che sia un'ora UTC, pertanto, in modo analogo quando `Kind` è `Utc`.|

La conversione viene utilizzata la formula seguente:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

In questa formula: 

- `NSDateReferenceDateTicks` viene calcolato in base il `NSDate` fanno riferimento a data UTC 00:00:00: 1 gennaio 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) è definito in [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Per creare il `NSDate` oggetto, il `TimeInterval` viene utilizzato con il `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) selettore.

#### <a name="nsdate-to-datetime"></a>NSDate a DateTime

La conversione da `NSDate` a `DateTime` utilizza la formula seguente:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

In questa formula: 

- `NSDateReferenceDateTicks` viene calcolato in base il `NSDate` fanno riferimento a data UTC 00:00:00: 1 gennaio 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) è definito in [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Al termine del calcolo `DateTimeTicks`, il `DateTime` [costruttore](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) viene richiamato, l'impostazione relativa `kind` a `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` può essere `nil`, ma un `DateTime` è uno struct in .NET, che, per definizione, non può essere `null`. Qualora il Licenziatario fornisca un `nil` `NSDate`, il valore predefinito verrà tradotto `DateTime` valore, che esegue il mapping a `DateTime.MinValue`.

`NSDate` supporta un massimo di versioni successivo e un valore minimo inferiore `DateTime`. Durante la conversione da `NSDate` a `DateTime`, questi valori superiori e inferiori vengono modificati per il `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) o [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), rispettivamente.
