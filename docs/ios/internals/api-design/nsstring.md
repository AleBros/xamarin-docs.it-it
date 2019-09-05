---
title: NSString in Novell. iOS e Novell. Mac
description: Questo documento descrive come Novell. iOS converte in modo trasparente gli oggetti NSString C# in oggetti stringa, quando ciò non accade.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 5fb34f9c5a880060d4f9677507e09969a876929b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291961"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString in Novell. iOS e Novell. Mac

La progettazione di Novell. iOS e Novell. Mac chiama l'API use per esporre il tipo di stringa .NET nativo, `string`, per la manipolazione delle stringhe in C# e altri linguaggi di programmazione .NET, e per esporre la stringa come tipo di dati esposto dall'API anziché tipo `NSString`di dati.

Ciò significa che gli sviluppatori non devono necessariamente tenere le stringhe destinate a essere usate per chiamare Novell. iOS & Novell. Mac API (Unified) in un tipo speciale (`Foundation.NSString`), possono sempre usare `System.String` mono per tutte le operazioni e ogni volta che un'API in Novell. iOS o Novell. Mac richiede una stringa, il binding API si occupa del marshalling delle informazioni.

Ad esempio, la proprietà "Text" di Objective-C su `UILabel` un di `NSString`tipo è dichiarata come segue:

```objc
@property(nonatomic, copy) NSString *text
```

Questo è esposto in Novell. iOS come:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Dietro le quinte, l'implementazione di questa proprietà esegue il C# marshalling della `NSString` stringa in un `objc_msgSend` oggetto e chiama il metodo in modo analogo a Objective-C.

Sono disponibili alcune API Objective-C di terze parti che non utilizzano un `NSString`, ma utilizzano invece una stringa C ("*char*"). In questi casi, è comunque possibile usare il C# tipo di dati String, ma è necessario usare l'attributo [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) per informare il generatore di binding che questa stringa non deve essere sottoposta a marshalling come `NSString`, ma invece come stringa C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Eccezioni alla regola

In Novell. iOS e Novell. Mac è stata creata un'eccezione a questa regola. La decisione tra il momento `string`in cui vengono esposte le istanze di e quando si crea un'eccezione ed espone `NSString` `NSString`, viene eseguita se il metodo può eseguire un confronto del puntatore anziché un confronto di contenuto.

Questo problema può verificarsi quando un'API Objective-C usa una `NSString`costante pubblica come token che rappresenta un'azione, anziché confrontare il contenuto effettivo della stringa.

In questi casi, `NSString`  le API vengono esposte ed è presente una minoranza di API. Si noterà anche che le proprietà NSString sono esposte in alcune classi. Queste `NSString` proprietà vengono esposte per elementi come le notifiche. Si tratta di proprietà che in genere hanno un aspetto simile al seguente:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Le notifiche sono chiavi utilizzate per la `NSNotification` classe quando si desidera eseguire la registrazione per un evento specifico trasmesso dal runtime.

Le chiavi in genere hanno un aspetto simile al seguente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un'altra posizione `NSString`in cui i vengono esposti nell'API è come token usati come parametri per determinate API in iOS o OS X che accettano `NSDictionary` oggetti come parametri. Il dizionario contiene `NSString` in genere chiavi. Novell. iOS, per convenzione, assegna un nome `NSString` a tali proprietà statiche aggiungendo il nome "Key".
