---
title: Procedure consigliate per l'incorporamento di .NET per Objective-C
description: Questo documento descrive le varie procedure consigliate per l'uso dell'incorporamento di .NET con Objective-C. Viene illustrata l'esposizione di un subset del codice gestito, l'esposizione di un'API chunkier, la denominazione e altro ancora.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: ff04c001193eb897aac81cdc66ed535c76d81717
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285110"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>Procedure consigliate per l'incorporamento di .NET per Objective-C

Si tratta di una bozza e potrebbe non essere sincronizzata con le funzionalità attualmente supportate dallo strumento. Ci auguriamo che questo documento si evolverà separatamente e che alla fine corrisponda allo strumento finale, ovvero verranno suggeriti approcci migliori a lungo termine, non soluzioni alternative immediate.

Una parte importante di questo documento si applica anche ad altre lingue supportate. Tuttavia, tutti gli esempi forniti C# sono in e Objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Esposizione di un subset del codice gestito

La libreria nativa/Framework generata contiene il codice Objective-C per chiamare ognuna delle API gestite esposte. Maggiore è la superficie dell'API (rende pubblico), più grande sarà la libreria _colla_ nativa.

Potrebbe essere consigliabile creare un assembly diverso, più piccolo, per esporre solo le API richieste allo sviluppatore nativo. Tale facciata consentirà inoltre di avere un maggiore controllo sulla visibilità, sulla denominazione e sul controllo degli errori... del codice generato.

## <a name="exposing-a-chunkier-api"></a>Esposizione di un'API chunkier

Il prezzo da pagare per la transizione da nativo a gestito (e viceversa). In questo modo, è preferibile esporre il _grosso anziché le API loquaci_ agli sviluppatori nativi, ad esempio

**Loquace**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Pesante**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Poiché il numero di transizioni è inferiore, le prestazioni saranno migliori. Richiede anche un minor numero di codice da generare, quindi questa operazione produrrà una libreria nativa più piccola.

## <a name="naming"></a>Denominazione

La denominazione di elementi è uno dei due problemi più complessi in ambito informatico, mentre gli altri vengono invalidati nella cache e si verificano errori off-by-1. Si spera che l'incorporamento di .NET possa proteggersi da tutti i nomi, ma

### <a name="types"></a>Tipi

Objective-C non supporta gli spazi dei nomi. In generale, i tipi sono preceduti dal prefisso 2 (per Apple) o 3 (per le terze parti), come `UIView` per la vista di UIKit, che denota il Framework.

Per i tipi .NET, non è possibile ignorare lo spazio dei nomi perché può presentare nomi duplicati o confusi. Ciò rende i tipi .NET esistenti molto lunghi, ad esempio

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

verrà usato come segue:

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

È tuttavia possibile riesporre il tipo come segue:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

per un uso più semplice di Objective-C, ad esempio:

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Metodi

Anche i nomi .NET validi potrebbero non essere ideali per un'API Objective-C.

Le convenzioni di denominazione in Objective-C sono diverse rispetto a .NET (maiuscole e minuscole anziché Pascal, più dettagliato).
Leggere le [linee guida per la codifica di Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Dal punto di vista di uno sviluppatore Objective-C, un metodo con `Get` prefisso implica che non si è proprietari dell'istanza, ad esempio la [regola Get](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Questa regola di denominazione non ha corrispondenze nel mondo GC .NET; un metodo .NET con `Create` prefisso si comporterà in modo identico in .NET. Tuttavia, per gli sviluppatori Objective-C, in genere significa che si è proprietari dell'istanza restituita, ovvero la [regola di creazione](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Eccezioni

In .NET è molto comune usare le eccezioni in modo estensivo per segnalare gli errori. Tuttavia, sono lenti e non identiche in Objective-C. Quando possibile, è necessario nasconderli dallo sviluppatore Objective-C.

Ad esempio, il modello `Try` .NET sarà molto più semplice da utilizzare per il codice Objective-C:

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

contro

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Eccezioni all'interno di`init*`

In .NET un costruttore deve avere esito positivo e restituire un'istanza valida (_auspicabile_) o generare un'eccezione.

Objective-C, invece, consente `init*` di restituire `nil` quando non è possibile creare un'istanza. Si tratta di un modello comune, ma non generale, usato in molti dei framework di Apple. In altri casi è possibile `assert` che si verifichi un errore (e termina il processo corrente).

Il generatore segue lo stesso `return nil` modello per i `init*` metodi generati. Se viene generata un'eccezione gestita, verrà stampata (usando `NSLog`) e `nil` verrà restituita al chiamante.

## <a name="operators"></a>Operatori

Objective-C non consente l'overload C# degli operatori, quindi questi vengono convertiti in selettori di classe.

I metodi denominati " [descrittivi"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) vengono generati in preferenza agli overload degli operatori quando vengono trovati e possono produrre un'API più semplice da utilizzare.

Le classi che eseguono l' `==` override `!=` degli operatori e\o devono eseguire l'override anche del metodo Equals (Object) standard.
