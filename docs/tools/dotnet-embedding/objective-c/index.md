---
title: Supporto per Objective-C
description: In questo documento viene fornita una descrizione del supporto per Objective-C nell'incorporamento di .NET. Viene illustrato il conteggio automatico dei riferimenti, NSString, protocolli, protocollo NSObject, eccezioni e altro ancora.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: e72f950dc6fcf12e70714e0fbb996ad5ea432548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029713"
---
# <a name="objective-c-support"></a>Supporto per Objective-C

## <a name="specific-features"></a>Funzionalità specifiche

La generazione di Objective-C presenta alcune funzionalità speciali che vale la pena notare.

### <a name="automatic-reference-counting"></a>Conteggio dei riferimenti automatici

Per chiamare i binding generati, è **necessario** utilizzare il conteggio automatico dei riferimenti (Arc). Il progetto che usa una libreria basata su incorporamento .NET deve essere compilato con `-fobjc-arc`.

### <a name="nsstring-support"></a>Supporto di NSString

Le API che espongono `System.String` tipi vengono convertite in `NSString`. In questo modo, la gestione della memoria risulta più semplice rispetto a quando si gestiscono `char*`.

### <a name="protocols-support"></a>Supporto protocolli

Le interfacce gestite vengono convertite in protocolli Objective-C in cui tutti i membri sono `@required`.

### <a name="nsobject-protocol-support"></a>Supporto del protocollo NSObject

Per impostazione predefinita, si presuppone che l'hash e l'uguaglianza predefiniti di .NET e del runtime di Objective-C siano intercambiabili, perché condividono una semantica simile.

Quando un tipo gestito esegue l'override di `Equals(Object)` o `GetHashCode`, in genere significa che il comportamento predefinito (.NET) non è sufficiente. Questo implica che è probabile che il comportamento di Objective-C predefinito non sia sufficiente.

In questi casi, il generatore esegue l'override del metodo [`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) e [`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) proprietà definite nel [protocollo di`NSObject`](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Ciò consente l'utilizzo trasparente da parte del codice Objective-C dell'implementazione gestita personalizzata.

### <a name="exceptions-support"></a>Supporto eccezioni

Se si passa `--nativeexception` come argomento a `objcgen`, le eccezioni gestite verranno convertite in eccezioni Objective-C che possono essere rilevate ed elaborate. 

### <a name="comparison"></a>Confronto

I tipi gestiti che implementano `IComparable` (o la versione generica `IComparable<T>`) produrranno metodi descrittivi Objective-C che restituiscono un `NSComparisonResult` e accettano un argomento `nil`. In questo modo l'API generata risulta più intuitiva per gli sviluppatori Objective-C. Esempio:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categories

I metodi delle estensioni gestite vengono convertiti in categorie. Ad esempio, i seguenti metodi di estensione su `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

creerebbe una categoria Objective-C come la seguente:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Quando un singolo tipo gestito estende più tipi, vengono generate più categorie Objective-C.

### <a name="subscripting"></a>Indice

Le proprietà indicizzate gestite vengono convertite in pedice di oggetti. Esempio:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

creerebbe Objective-C analogo al seguente:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

che può essere usato tramite la sintassi di indice Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

A seconda del tipo di indicizzatore, verrà generato un indice indicizzato o con chiave quando appropriato.

Questo [articolo](https://nshipster.com/object-subscripting/) è un'ottima introduzione all'indicizzazione.

## <a name="main-differences-with-net"></a>Principali differenze con .NET

### <a name="constructors-vs-initializers"></a>Costruttori e inizializzatori

In Objective-C è possibile chiamare qualsiasi prototipo di inizializzatore di qualsiasi classe padre nella catena di ereditarietà, a meno che non sia contrassegnato come non disponibile (`NS_UNAVAILABLE`).

In C# è necessario dichiarare in modo esplicito un membro del costruttore all'interno di una classe, il che significa che i costruttori non vengono ereditati.

Per esporre la rappresentazione corretta dell' C# API a Objective-C,`NS_UNAVAILABLE`viene aggiunto a qualsiasi inizializzatore che non è presente nella classe figlio dalla classe padre.

C#API

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

API di Surface Objective-C:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

`initWithId:` è stato contrassegnato come non disponibile.

### <a name="operator"></a>??

Objective-C non supporta l'overload C# degli operatori, quindi gli operatori vengono convertiti in selettori di classe:

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

Tuttavia, alcuni linguaggi .NET non supportano l'overload degli operatori, quindi è comune includere anche un metodo denominato ["descrittivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) oltre all'overload dell'operatore.

Se vengono rilevate sia la versione dell'operatore che la versione "descrittiva", verrà generata solo la versione descrittiva, che genererà lo stesso nome Objective-C.

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

diventa

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operatore di uguaglianza

In General operator `==` in C# viene gestito come un operatore generale come indicato in precedenza.

Tuttavia, se viene trovato l'operatore "friendly" uguale a, sia l'operatore `==` che l'operatore `!=` verranno ignorati nella generazione.

### <a name="datetime-vs-nsdate"></a>DateTime rispetto a NSDate

Dalla documentazione [`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc) :

> gli oggetti `NSDate` incapsulano un singolo momento, indipendentemente da qualsiasi sistema calendaristiche o fuso orario specifico. Gli oggetti data non sono modificabili, rappresentando un intervallo di tempo invariante rispetto a una data di riferimento assoluta (00:00:00 UTC il 1 ° gennaio 2001).

A causa `NSDate` data di riferimento, tutte le conversioni tra di essa e `DateTime` devono essere eseguite in formato UTC.

#### <a name="datetime-to-nsdate"></a>Da DateTime a NSDate

Quando si esegue la conversione da `DateTime` a `NSDate`, viene presa in considerazione la proprietà `Kind` in `DateTime`:

|Tipo|Risultati|
|---|---|
|`Utc`|La conversione viene eseguita utilizzando l'oggetto `DateTime` fornito così com'è.|
|`Local`|Il risultato della chiamata di `ToUniversalTime()` nell'oggetto `DateTime` fornito viene usato per la conversione.|
|`Unspecified`|Si presuppone che l'oggetto `DateTime` fornito sia UTC, quindi lo stesso comportamento quando `Kind` è `Utc`.|

La conversione utilizza la formula seguente:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

In questa formula: 

- `NSDateReferenceDateTicks` viene calcolato in base alla data di riferimento `NSDate` 00:00:00 UTC il 1 gennaio 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) viene definito in [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Per creare l'oggetto `NSDate`, viene usato il `TimeInterval` con il selettore `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) .

#### <a name="nsdate-to-datetime"></a>Da NSDate a DateTime

La conversione da `NSDate` a `DateTime` utilizza la formula seguente:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

In questa formula: 

- `NSDateReferenceDateTicks` viene calcolato in base alla data di riferimento `NSDate` 00:00:00 UTC il 1 gennaio 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) viene definito in [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Dopo aver calcolato `DateTimeTicks`, viene richiamato il [costruttore](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) `DateTime`, impostando la relativa `kind` su `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` possono essere `nil`, ma un `DateTime` è uno struct in .NET, che per definizione non può essere `null`. Se si assegna un `nil` `NSDate`, verrà convertito nel valore di `DateTime` predefinito, che esegue il mapping a `DateTime.MinValue`.

`NSDate` supporta un valore massimo superiore e un valore minimo inferiore rispetto a `DateTime`. Quando si esegue la conversione da `NSDate` a `DateTime`, questi valori più alti e inferiori vengono modificati rispettivamente nel `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) o [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue).
