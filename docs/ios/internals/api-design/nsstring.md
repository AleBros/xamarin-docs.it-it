---
title: NSString in Xamarin.iOS e Xamarin.Mac
description: In questo documento viene descritto come Xamarin.iOS converte in modo trasparente NSString oggetti in oggetti stringa C , quando questo non accade.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022355"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString in Xamarin.iOS e Xamarin.Mac

La progettazione di Xamarin.iOS e Xamarin.Mac richiede l'API di utilizzo `string`per esporre il tipo di stringa .NET nativo, , per la modifica delle `NSString` stringhe in linguaggi di programmazione .NET e per esporre la stringa come tipo di dati esposto dall'API anziché il tipo di dati.

Ciò significa che gli sviluppatori non devono mantenere le stringhe che devono essere utilizzate per chiamare Xamarin.iOS &`Foundation.NSString`Xamarin.Mac API `System.String` (Unified) in un tipo speciale ( ), possono continuare a utilizzare Mono per tutte le operazioni e ogni volta che un'API in Xamarin.iOS o Xamarin.Mac richiede una stringa, il nostro binding API si occupa di effettuare il marshalling delle informazioni.

Ad esempio, la proprietà Objective-C `UILabel` "text" in un tipo `NSString`, viene dichiarata in questo modo:

```objc
@property(nonatomic, copy) NSString *text
```

Questo è esposto in Xamarin.iOS come:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Dietro le quinte, l'implementazione di questa proprietà `NSString` esegue `objc_msgSend` il marshalling della stringa di C , in un e chiama il metodo nello stesso modo in cui Objective-C.

Esistono una manciata di API Objective-C di terze `NSString`parti che non utilizzano un oggetto , ma utilizzano invece una stringa C (un "*char*"). In questi casi, è comunque possibile utilizzare il tipo di dati stringa C , ma è necessario utilizzare l'attributo [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) per informare il generatore di associazione che questa stringa non deve essere sottoposta a marshalling come `NSString`, ma come stringa C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Eccezioni alla regola

Sia in Xamarin.iOS che in Xamarin.Mac, abbiamo fatto un'eccezione a questa regola. La decisione tra `string`quando esponiamo s, `NSString`e quando facciamo `NSString` un except e expose s, viene presa se il metodo potrebbe eseguire un confronto del puntatore invece di un confronto del contenuto.

Ciò può verificarsi quando un Objective-C API usa una costante pubblica `NSString` come token che rappresenta un'azione, anziché confrontare il contenuto effettivo della stringa.

In questi `NSString`  casi, le API sono esposte e sono presenti una minoranza di API con questo. Si noterà inoltre che NSString proprietà sono esposte in alcune classi. Tali `NSString` proprietà sono esposte per elementi come le notifiche. Queste sono proprietà di solito assomigliano a questo:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Le notifiche sono chiavi `NSNotification` utilizzate per la classe quando si desidera registrarsi per un determinato evento trasmesso dal runtime.

Le chiavi di solito hanno un aspetto simile al seguente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Un'altra `NSString`posizione in cui s sono esposti nell'API è come token che vengono utilizzati `NSDictionary` come parametri per determinate API in iOS o OS X che accettano oggetti come parametri. Il dizionario `NSString` contiene in genere le chiavi. Xamarin.iOS, per convenzione, `NSString` denomina tali proprietà statiche aggiungendo il nome "Key".
