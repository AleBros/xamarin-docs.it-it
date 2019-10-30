---
title: NSString in Novell. iOS e Novell. Mac
description: Questo documento descrive come Novell. iOS converte in modo trasparente gli oggetti NSString C# in oggetti stringa, quando ciò non accade.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022355"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString in Novell. iOS e Novell. Mac

La progettazione di Novell. iOS e Novell. Mac chiama l'API use per esporre il tipo di stringa .NET nativo, `string`, per la manipolazione delle stringhe in C# e altri linguaggi di programmazione .NET e per esporre la stringa come tipo di dati esposto dall'API anziché `NSString` tipo di dati.

Ciò significa che gli sviluppatori non devono necessariamente tenere le stringhe destinate a essere usate per chiamare Novell. iOS & API Novell. Mac (unificata) in un tipo speciale (`Foundation.NSString`), possono sempre usare il `System.String` di mono per tutte le operazioni e ogni volta che un'API in Per Novell. iOS o Novell. Mac è necessaria una stringa, il binding API si occupa del marshalling delle informazioni.

La proprietà "Text" di Objective-C in un `UILabel` di tipo `NSString`, ad esempio, è dichiarata come segue:

```objc
@property(nonatomic, copy) NSString *text
```

Questo è esposto in Novell. iOS come:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Dietro le quinte, l'implementazione di questa proprietà esegue il C# marshalling della stringa in un`NSString`e chiama il metodo`objc_msgSend`nello stesso modo in cui è presente Objective-C.

Sono disponibili alcune API Objective-C di terze parti che non utilizzano un `NSString`, ma utilizzano invece una stringa C ("*char*"). In questi casi, è comunque possibile usare il C# tipo di dati String, ma è necessario usare l'attributo [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) per informare il generatore di binding che questa stringa non deve essere sottoposta a marshalling come`NSString`, ma invece come stringa C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Eccezioni alla regola

In Novell. iOS e Novell. Mac è stata creata un'eccezione a questa regola. La decisione tra il momento in cui si espongono `string`s e quando si crea un'eccezione ed espone `NSString`s, viene eseguita se il metodo di  `NSString`potrebbe eseguire un confronto del puntatore anziché un confronto di contenuto.

Questo problema può verificarsi quando un'API Objective-C usa un `NSString`pubblico costante come token che rappresenta un'azione, anziché confrontare il contenuto effettivo della stringa.

In questi casi, vengono esposte `NSString`API ed è presente una minoranza di API. Si noterà anche che le proprietà NSString sono esposte in alcune classi. Tali proprietà `NSString` vengono esposte per elementi come le notifiche. Si tratta di proprietà che in genere hanno un aspetto simile al seguente:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Le notifiche sono chiavi utilizzate per la classe `NSNotification` quando si desidera eseguire la registrazione per un evento specifico trasmesso dal runtime.

Le chiavi in genere hanno un aspetto simile al seguente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un'altra posizione in cui `NSString`s sono esposte nell'API è come token usati come parametri per determinate API in iOS o OS X che accettano `NSDictionary` oggetti come parametri. Il dizionario contiene in genere chiavi `NSString`. Novell. iOS, per convenzione, assegna un nome alle proprietà `NSString` statiche aggiungendo il nome "Key".
