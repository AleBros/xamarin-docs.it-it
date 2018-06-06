---
title: NSString in xamarin. IOS e Xamarin.Mac
description: Questo documento descrive come xamarin. IOS in modo trasparente converte NSString oggetti c# oggetti stringa, quando ciò si verifichi.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: baf36700ab4d608296a9a67e234ce613da9ca077
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786090"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString in xamarin. IOS e Xamarin.Mac

La progettazione di xamarin. IOS e Xamarin.Mac chiami l'API di utilizzo per esporre il tipo nativo .NET stringa, `string`, per la modifica delle stringhe in c# e altri linguaggi di programmazione .NET e per esporre stringa come il tipo di dati tramito l'API anziché le `NSString` tipo di dati.

Ciò significa che gli sviluppatori non necessario mantenere le stringhe che servono da utilizzare per la chiamata a xamarin. IOS e l'API di Xamarin.Mac (unificato) in un tipo speciale (`Foundation.NSString`), è possibile continuare a utilizzare del Mono `System.String` per tutte le operazioni e ogni volta che un'API in xamarin. IOS o Xamarin.Mac richiede una stringa, l'associazione API gestisce le informazioni di marshalling.

Ad esempio, la proprietà "text" Objective-C in un `UILabel` di tipo `NSString`, viene dichiarata come segue:

```objc
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
