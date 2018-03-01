---
title: NSString
ms.topic: article
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: b52a9e926f5ec907746d2a2dd8ee7a6a6212742f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="nsstring"></a>NSString

La progettazione di xamarin e Xamarin.Mac chiami l'API di utilizzo per esporre il tipo nativo .NET stringa, `string`, per la modifica delle stringhe in c# e altri linguaggi di programmazione .NET e per esporre una stringa come tipo di dati esposto dall'API anziché il `NSString` tipo di dati.


Ciò significa che gli sviluppatori non necessario mantenere le stringhe che servono da utilizzare per la chiamata a xamarin. IOS e l'API di Xamarin.Mac (unificato) in un tipo speciale (`Foundation.NSString`), è possibile continuare a utilizzare del Mono `System.String` per tutte le operazioni e ogni volta che un'API in xamarin. IOS o Xamarin.Mac richiede una stringa, l'associazione API gestisce le informazioni di marshalling.

Ad esempio, la proprietà "text" Objective-C in un `UILabel` di tipo `NSString`, viene dichiarata come segue:

```csharp
@property(nonatomic, copy) NSString *text
```

Questa funzionalità è esposta in xamarin. IOS come:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Dietro le quinte, l'implementazione di questa proprietà esegue il marshalling la stringa in c# un `NSString` e chiama il `objc_msgSend` metodo nello stesso modo che verrebbe Objective-C.

Esistono un numero limitato di API Objective-C di terze parti che non utilizzano un `NSString`, ma utilizzare invece una stringa C (un "*char*"). In questi casi, è comunque possibile utilizzare il tipo di dati stringa in c#, ma è necessario utilizzare il [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) attributo per informare il generatore di associazione che questa stringa deve effettuare il marshalling come un `NSString`, ma come una stringa C.

 <a name="Exceptions_to_the_Rule" />


## <a name="exceptions-to-the-rule"></a>Eccezioni alla regola

In xamarin. IOS e Xamarin.Mac, sono stati introdotti un'eccezione a questa regola. La decisione tra quando è necessario esporre `string`s, e quando si effettua un tranne ed esporre `NSString`s, viene eseguita se il `NSString` metodo Impossibile eseguendo un confronto tra puntatori anziché un confronto del contenuto.


Questo problema può verificarsi quando un'API Objective-C viene utilizzato un pubblico `NSString` costante come un token che rappresenta un'azione, anziché confrontare il contenuto effettivo della stringa.


In questi casi, `NSString` le API esposte e sono presenti una minoranza di API che dispongono di questo. Si noterà che la proprietà NSString siano esposti in alcune classi. Quelli `NSString` proprietà vengono esposte per elementi quali le notifiche. Queste sono le proprietà in genere simile al seguente:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Le notifiche sono le chiavi usate per la `NSNotification` classe quando si desidera registrare per un particolare evento vengono trasmesso dal runtime.

Le chiavi in genere un aspetto simile al seguente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un'altra posizione in cui `NSString`s vengono esposte nell'API è come i token utilizzati come parametri per alcune API di iOS o OS X che accettano `NSDictionary` gli oggetti come parametri. Il dizionario contiene in genere `NSString` chiavi. Xamarin. IOS, per convenzione, i nomi quelli statici `NSString` proprietà aggiungendo il nome "Key".
