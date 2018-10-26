---
title: NSString in xamarin. IOS e xamarin. Mac
description: Questo documento descrive come xamarin. IOS converte in modo trasparente NSString oggetti da C# stringa di oggetti, quando ciò si verifichi.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: cc9e3f992642f3cdc3d16fe6f829b6a6c06b50fc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115035"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString in xamarin. IOS e xamarin. Mac

La progettazione di xamarin. IOS e xamarin. Mac chiami l'API di utilizzo per esporre il tipo di stringa .NET native, `string`, per la modifica in C# e altri linguaggi di programmazione .NET ed esporre stringa come tipo di dati esposto dall'API invece di il `NSString` tipo di dati.

Questo significa che gli sviluppatori non necessario mantenere le stringhe che devono essere utilizzati per la chiamata a xamarin. IOS e l'API xamarin. Mac (unificato) in un tipo speciale (`Foundation.NSString`), è possibile continuare a usare Mono `System.String` per tutte le operazioni e ogni volta che un'API in xamarin. Mac o xamarin. IOS richiede una stringa, l'associazione API gestisce le informazioni di marshalling.

Ad esempio, la proprietà "text" Objective-C in una `UILabel` di tipo `NSString`, viene dichiarata nel modo seguente:

```objc
@property(nonatomic, copy) NSString *text
```

Questa funzionalità è esposta in xamarin. IOS come:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Dietro le quinte, l'implementazione di questa proprietà esegue il marshalling di C# stringa in un `NSString` e chiama il `objc_msgSend` metodo di stesso modo in cui verrebbe Objective-C.

Esistono una vasta gamma di API Objective-C di terze parti che non usano un `NSString`, ma utilizzare invece una stringa C (una "*char*"). In questi casi, è comunque possibile usare il C# tipo di dati, stringa, ma è necessario usare il [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) attributo per informare il generatore di binding che questa stringa dovrebbe non essere effettuato il marshalling come un' `NSString`, anziché come una stringa C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Eccezioni alla regola

In xamarin. IOS e xamarin. Mac, sono stati apportati un'eccezione a questa regola. La decisione tra quando è necessario esporre `string`s, e quando si aggiunge un tranne ed esporre `NSString`s, viene eseguita se il `NSString` metodo è stato possibile eseguire un confronto tra puntatori anziché un confronto del contenuto.

Questo problema può verificarsi quando un'API Objective-C Usa pubblico `NSString` costante come un token che rappresenta un'azione, invece di confrontare il contenuto effettivo della stringa.

In questi casi `NSString`  vengono esposte le API e sono presenti una minoranza di API che dispongono di questo. Si noterà anche che le proprietà NSString sono esposte in alcune classi. Quelli `NSString` proprietà esposte per elementi quali le notifiche. Queste sono le proprietà in genere simile al seguente:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```
Le notifiche sono le chiavi usate per il `NSNotification` classe quando si desidera effettuare la registrazione per un determinato evento viene trasmesso dal runtime.

Le chiavi in genere un aspetto simile al seguente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un'altra posizione in cui `NSString`s vengono esposte nell'API è come i token usati come parametri per determinate API in iOS o OS X che accettano `NSDictionary` oggetti come parametri. Il dizionario contiene in genere `NSString` chiavi. Xamarin. IOS, per convenzione, nomi di quelli statici `NSString` proprietà aggiungendo il nome di "Chiave".
