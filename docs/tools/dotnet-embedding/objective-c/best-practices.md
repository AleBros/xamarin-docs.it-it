---
title: Incorporamento di .NET le procedure consigliate per Objective-C
description: Questo documento descrive varie procedure consigliate per l'uso di .NET incorporamento con Objective-C. Illustra che espone un subset del codice gestito, che espone un'API chunkier, denominazione e altro ancora.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 33138b7858b8bc04a5be30f9fad1709e916f5575
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105401"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>Incorporamento di .NET le procedure consigliate per Objective-C

Si tratta di una bozza e potrebbero non essere sincronizzate con le funzionalità attualmente supportate dallo strumento. Ci auguriamo che questo documento verrà evoluzione separata e corrisponde alla fine lo strumento finale, ovvero sarà suggeriamo approcci migliori a lungo termine - soluzioni alternative non immediate.

Gran parte di questo documento vale anche per altri linguaggi supportati. Tuttavia tutti forniti esempi sono disponibili in C# e Objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Esposizione di un subset del codice gestito

Il libreria/framework nativo generato contiene codice Objective-C per chiamare ognuna delle API gestite che viene esposto. Le API più della superficie di attacco (Rendi pubblico) quindi più grande nativi _glue_ libreria diventerà.

Potrebbe essere una buona idea per creare un assembly diverso, più piccolo, per esporre solo le API necessarie per lo sviluppatore nativo. Tale interfaccia consentirà anche di maggiore controllo sulla visibilità, denominazione, errore durante la verifica... il codice generato.

## <a name="exposing-a-chunkier-api"></a>Che espone un'API chunkier

C'è un prezzo da pagare per la transizione da nativo a gestito (e back-). Di conseguenza, è meglio esporre _pesante invece di "frammentate"_ API per gli sviluppatori nativi, ad esempio,

**"Frammentato"**

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

Poiché il numero di transizioni è più piccolo le prestazioni risulteranno migliorate. Richiede anche meno codice da generare, pertanto si otterrà anche una libreria nativa più piccola.

## <a name="naming"></a>Denominazione

Denominazione di elementi è uno dei due problemi più difficili in scienze informatiche, gli altri in fase di errori off-da-1 e invalidamento della cache. Si spera .NET incorporamento possibile proteggersi da tutto tranne di denominazione.

### <a name="types"></a>Tipi

Objective-C non supporta gli spazi dei nomi. In generale, nei relativi tipi sono preceduti da un 2 (per Apple) o 3 (per le parti 3rd) carattere di prefisso, ad esempio `UIView` per la visualizzazione del UIKit, che indica il framework.

Per i tipi .NET ignorare lo spazio dei nomi perché non può introdurre duplicati o poco chiaro, i nomi. In questo modo, ad esempio tipi .NET esistenti molto lunghi,

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

potrebbe essere utilizzati come:

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

È tuttavia possibile esporre nuovamente il tipo come:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

rendendo più semplice di Objective-C da usare, ad esempio:

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Metodi

I nomi di .NET anche i tempi potrebbero non essere ideali per un'API Objective-C.

Convenzioni di denominazione in Objective-C sono diverse da .NET (le iniziali minuscole anziché maiuscole minuscole pascal, più dettagliato).
Leggi i [linee guida per Cocoa di codifica](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Da punto di vista di uno sviluppatore di Objective-C, un metodo con un `Get` prefisso implica non si è proprietari dell'istanza, ad esempio il [ottenere regola](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Questa regola di denominazione non ha corrispondenze nel mondo .NET Garbage Collector; un metodo .NET con un `Create` prefisso si comporterà in modo identico in .NET. Tuttavia, per gli sviluppatori Objective-C, in genere significa che si è proprietari di istanza restituita, ovvero il [creare regola](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Eccezioni

È piuttosto comune in .NET per utilizzare le eccezioni in modo esteso per segnalare gli errori. Tuttavia, sono lente e non abbastanza identici in Objective-C. Laddove possibile è consigliabile nascondere allo sviluppatore di Objective-C.

Ad esempio, .NET `Try` motivo sarà molto più facile da utilizzare dal codice Objective-C:

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

rispetto a

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Eccezioni all'interno di `init*`

In .NET un costruttore necessario riuscire e restituire un (_spera_) genera un'eccezione o istanza valido.

Al contrario, Objective-C consente `init*` da restituire `nil` quando non è possibile creare un'istanza. Questo è un modello comune, ma non generale, usato nella maggior parte dei framework di Apple. In altri casi un `assert` possono verificarsi (e terminare il processo corrente).

Il generatore di seguire gli stessi `return nil` pattern per generati `init*` metodi. Se viene generata un'eccezione gestita, allora verrà stampato (tramite `NSLog`) e `nil` verrà restituito al chiamante.

## <a name="operators"></a>Operatori

Objective-C non consente gli operatori in overload come C# caso affermativo, questi vengono convertiti in selettori di classe.

["Descrittivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) vengono generati metodi denominati anziché l'overload dell'operatore quando disponibili e possono produrre una più facile da utilizzare API.

Le classi che eseguono l'override gli operatori `==` e/o elaborati `!=` l'override anche del metodo Equals (Object) standard.
