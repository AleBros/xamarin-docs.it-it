---
title: Supporto di Objective-C
ms.topic: article
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 047f7d7497a114bf4b7c94e50bdf09862b882794
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="objective-c-support"></a>Supporto di Objective-C

## <a name="specific-features"></a>Funzionalità specifiche

La generazione di ObjC è una funzionalità speciali, alcuni vale la pena sottolineare.

### <a name="automatic-reference-counting"></a>Conteggio dei riferimenti automatico

L'uso di automatico riferimento conteggio ARC () è **obbligatorio** per chiamare le associazioni generate. Utilizzo di una raccolta basata su embeddinator progetto deve essere compilato con `-fobjc-arc`.

### <a name="nsstring-support"></a>Supporto NSString

Le API che espongono `System.String` tipi vengono convertiti in `NSString`. In questo modo più semplice rispetto all'utilizzo di gestione della memoria `char*`.

### <a name="protocols-support"></a>Supporto di protocolli

Interfacce gestite vengono convertite in protocolli ObjC in cui tutti i membri sono `@required`.

### <a name="nsobject-protocol-support"></a>Supporto del protocollo NSObject

Per impostazione predefinita, si presuppone l'hash dell'indirizzo predefinito e verificarne l'uguaglianza di .net e il runtime ObjC sono intercambiabili e di fine che condividano semantica molto simile.

Quando esegue l'override di un tipo gestito `Equals(Object)` o `GetHashCode` in genere significa che il comportamento predefinito (.NET) non è più adatto. Possiamo presupporre che potrebbe non essere il comportamento di Objective-C predefinito.

In questo caso il generatore esegue l'override di [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) (metodo) e [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) definita nella proprietà il [ `NSObject` protocollo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). In questo modo l'implementazione gestita personalizzata da utilizzare in modo trasparente dal codice ObjC.

### <a name="comparison"></a>Confronto

I tipi che implementano gestiti `IComparable` o è una versione generica `IComparable<T>` produrrà metodi descrittivi ObjC che restituisce un `NSComparisonResult` e accettare un `nil` argomento. In questo modo, API generata più semplice per gli sviluppatori ObjC, ad esempio

```csharp
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categories

Estensioni di metodi vengono convertiti in categorie gestito. Ad esempio i seguenti metodi di estensione su `Collection`:

```csharp
    public static class SomeExtensions {

        public static int CountNonNull (this Collection collection) { ... }

        public static int CountNull (this Collection collection) { ... }
    }
```

creare una categoria di Objective-C come questa:

```csharp
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;
@end
```

Quando un singolo gestito tipo estende diversi tipi, quindi vengono generate più categorie Objective-C.

### <a name="subscripting"></a>Indice

Proprietà indicizzate gestite vengono convertite nell'indice di oggetto. Ad esempio:

```csharp
    public bool this[int index] {
        get { return c[index]; }
        set { c[index] = value; }
    }
```

creare Objective-C simile a:

```csharp
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

che può essere usato tramite la sintassi di indice Objective-C:

```csharp
    if ([intCollection [0] isEqual:@42])
        intCollection[0] = @13;
```

A seconda del tipo dell'indicizzatore, verrà generato l'indice di chiave o indicizzata dove appropriato.

Questo [articolo](http://nshipster.com/object-subscripting/) rappresenta un ottimo inizio per l'indice.

## <a name="main-differences-with-net"></a>Differenze principali con .NET

### <a name="constructors-vs-initializers"></a>Costruttori e inizializzatori

In Objective-C, è possibile chiamare qualsiasi dell'inizializzatore di prototipi di una qualsiasi delle classi padre nella catena di ereditarietà a meno che non è contrassegnato come non disponibile (NS_UNAVAILABLE).

In c# è necessario dichiarare in modo esplicito un membro di costruttore all'interno di una classe, ciò significa che non vengono ereditati costruttori.

Per esporre la rappresentazione di destra dell'API c# per Objective-C, si aggiungono `NS_UNAVAILABLE` a qualsiasi inizializzatore che non è presente nella classe figlio dalla classe padre.

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

```objectivec
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Qui è possibile osservare che `initWithId:` è stato contrassegnato come non disponibile.

### <a name="operator"></a>Operatore

ObjC non supporta l'operatore di overload come c#, pertanto gli operatori vengono convertiti in selettori di classe:

```csharp
    public static AllOperators operator + (AllOperators c1, AllOperators c2)
    {
        return new AllOperators (c1.Value + c2.Value);
    }
```

in

```csharp
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Tuttavia, alcuni linguaggi .NET non supportano overload degli operatori, pertanto è comune includere inoltre un ["descrittivo"](https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx) denominato metodo oltre l'overload dell'operatore.

Se sia la versione di operatore e la versione "descrittivo" vengono trovati, verrà generata solo la versione descrittiva come generano lo stesso nome objective-c.

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

```csharp
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operatore di uguaglianza

In generale operatore = = c# viene gestita come un operatore generale come indicato sopra.

Tuttavia, se viene trovato l'operatore uguale a "descrittivo", entrambi operatore = = e l'operatore! = verrà ignorato nella generazione.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

Da [del NSDate](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentazione:

> Oggetti NSDate incapsulano un singolo punto nel tempo, indipendente da qualsiasi sistema calendrical particolare o di un fuso orario. Gli oggetti di data non sono modificabili, che rappresenta un intervallo di tempo invariante rispetto alla data di un riferimento assoluto (00:00:1 gennaio 2001 00 UTC).

A causa di `NSDate` fanno riferimento a data, tutte le conversioni tra questo e `DateTime` deve essere eseguita in formato UTC.

#### <a name="datetime-to-nsdate"></a>DateTime per NSDate

Durante la conversione da `DateTime` a `NSDate` il valore DateTime `Kind` proprietà viene preso in considerazione.

|Kind|Risultati                                                                                            |
|---|---|
|Ora UTC|La conversione viene eseguita utilizzando l'oggetto fornito `DateTime` dell'oggetto come.|
|Locale|Il risultato della chiamata al metodo `ToUniversalTime()` nell'oggetto fornito `DateTime` oggetto viene utilizzato per la conversione.|
|Non specificato|L'oggetto fornito `DateTime` oggetto presuppone che sia un'ora UTC, in modo analogo a tipo = = (UTC).|

La conversione viene eseguita utilizzando la formula seguente:

> [!NOTE]
> **TimeInterval** = DateTimeObjectTicks - NSDateReferenceDateTicks[dt] / [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx)

Una volta ottenuto il TimeInterval utilizziamo del NSDate [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) selettore per la sua creazione.

#### <a name="nsdate-to-datetime"></a>NSDate a DateTime

Passando da NSDate a DateTime si presuppone che ci stiamo ottenere un NSDate istanza che è una data di riferimento è **00:00:00 UTC dell'1 gennaio 2001** e utilizzare la formula seguente:

> [!NOTE]
> **DateTimeTicks** = NSDateTimeIntervalSinceReferenceDate * [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx) + NSDateReferenceDateTicks[dt]

Dopo che è stata calcolata la **DateTimeTicks** è utilizzare il seguente valore di DateTime [costruttore](https://msdn.microsoft.com/en-us/library/w0d47c9c(v=vs.110).aspx) impostazione relativo `kind` a `DateTimeKind.Utc`.

Esistono alcune considerazioni che è necessario essere consapevoli dei, può essere NSDate `nil` ma un valore DateTime è uno struct in .NET e per definizione non può essere `null`. Se si fornisce un `nil` NSDate si può verrà convertito il valore predefinito DateTime che esegue il mapping a `DateTime.MinValue`.

MinValue e MaxValue sono diversi, NSDate può supportare i limiti superiori e inferiori al valore di DateTime, pertanto ogni volta che si assegnata un valore maggiore o inferiore è verrà impostato su DateTime [MaxValue](https://msdn.microsoft.com/en-us/library/system.datetime.maxvalue(v=vs.110).aspx) o [MinValue](https://msdn.microsoft.com/en-us/library/system.datetime.minvalue(v=vs.110).aspx) rispettivamente.

**DT**: data di riferimento NSDate **00:00:00 UTC dell'1 gennaio 2001** => `new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;`
