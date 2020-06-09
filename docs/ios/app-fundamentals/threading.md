---
title: Threading in Novell. iOS
description: Questo documento descrive come usare le API System. Threading in un'applicazione Novell. iOS. Viene illustrato il Task Parallel Library, la compilazione di applicazioni reattive e Garbage Collection.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 0de7fcd5af9e0338679893b3d7fde073c5274365
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567720"
---
# <a name="threading-in-xamarinios"></a>Threading in Novell. iOS

Il runtime di Novell. iOS consente agli sviluppatori di accedere alle API di Threading .NET, in modo esplicito quando si usano i thread ( `System.Threading.Thread, System.Threading.ThreadPool` ) e in modo implicito quando si usano i modelli di delegati asincroni o i metodi BeginXxx, nonché l'intera gamma di API che supportano il Task Parallel Library.

Novell consiglia vivamente di usare la [Task Parallel Library](https://msdn.microsoft.com/library/dd460717.aspx) (TPL) per la creazione di applicazioni per alcuni motivi:

- L'utilità di pianificazione TPL predefinita delegherà l'esecuzione dell'attività al pool di thread, che a sua volta aumenterà in modo dinamico il numero di thread necessari durante l'esecuzione del processo, evitando uno scenario in cui un numero eccessivo di thread si trova in competizione per il tempo di CPU. 
- È più facile pensare alle operazioni in termini di attività TPL. È possibile modificarli facilmente, pianificarli, serializzare l'esecuzione o avviare molti in parallelo con un set completo di API. 
- Si tratta della base per la programmazione con le nuove estensioni del linguaggio asincrono C#. 

Il pool di thread aumenterà lentamente il numero di thread in base alle esigenze, in base al numero di core della CPU disponibili nel sistema, al carico del sistema e all'applicazione. È possibile usare questo pool di thread richiamando i metodi in `System.Threading.ThreadPool` o usando il valore predefinito `System.Threading.Tasks.TaskScheduler` (parte dei *Framework paralleli*).

In genere, gli sviluppatori usano i thread quando devono creare applicazioni reattive e non vogliono bloccare il ciclo di esecuzione principale dell'interfaccia utente.

 <a name="Developing_Responsive_Applications"></a>

## <a name="developing-responsive-applications"></a>Sviluppo di applicazioni reattive

L'accesso agli elementi dell'interfaccia utente deve essere limitato allo stesso thread che esegue il ciclo principale per l'applicazione. Se si desidera apportare modifiche all'interfaccia utente principale da un thread, è necessario accodare il codice usando [NSObject. InvokeOnMainThread](xref:Foundation.NSObject), come segue:

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

Il codice precedente richiama il codice all'interno del delegato nel contesto del thread principale, senza causare race condition che potrebbero causare l'arresto anomalo dell'applicazione.

 <a name="Threading_and_Garbage_Collection"></a>

## <a name="threading-and-garbage-collection"></a>Threading e Garbage Collection

Nel corso dell'esecuzione, il runtime di Objective-C creerà e rilascerà oggetti. Se gli oggetti sono contrassegnati per la "versione automatica", il runtime di Objective-C rilascerà tali oggetti all'oggetto corrente del thread `NSAutoReleasePool` . Novell. iOS crea un `NSAutoRelease` pool per ogni thread da `System.Threading.ThreadPool` e per il thread principale. Questa operazione per estensione copre tutti i thread creati usando il TaskScheduler predefinito in System. Threading. Tasks.

Se si creano thread personalizzati usando `System.Threading` , è necessario fornire `NSAutoRelease` il proprio pool per evitare la perdita di dati. A tale scopo, è sufficiente eseguire il wrapping del thread nel seguente codice:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Nota: a partire da Novell. iOS 5,2, non è più necessario fornire il proprio `NSAutoReleasePool` nome, perché ne verrà fornito automaticamente uno.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo del thread dell'interfaccia utente](~/ios/user-interface/ios-ui/ui-thread.md)
