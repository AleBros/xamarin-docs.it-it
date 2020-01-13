---
title: Threading in Xamarin.iOS
description: Questo documento descrive come usare le API System. Threading in un'applicazione Xamarin.iOS. Viene illustrato il Task Parallel Library, la compilazione di applicazioni reattive e Garbage Collection.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 1c9282c790aa5436667b37e1861a96afffcaa668
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009443"
---
# <a name="threading-in-xamarinios"></a>Threading in Xamarin.iOS

Il runtime di Xamarin.iOS consente agli sviluppatori di accedere alle API di Threading .NET in modo esplicito quando si usano i thread (`System.Threading.Thread, System.Threading.ThreadPool`) e in modo implicito quando si usano i modelli delegati asincroni o i metodi BeginXXX, nonché l'intera gamma di API che supportano l'attività Libreria parallela.

Novell consiglia vivamente di usare la [Task Parallel Library](https://msdn.microsoft.com/library/dd460717.aspx) (TPL) per la creazione di applicazioni per alcuni motivi:

- L'utilità di pianificazione TPL predefinita delegherà l'esecuzione dell'attività al pool di thread, che a sua volta aumenterà in modo dinamico il numero di thread necessari durante l'esecuzione del processo, evitando uno scenario in cui un numero eccessivo di thread si trova in competizione per il tempo di CPU. 
- È più facile pensare alle operazioni in termini di attività TPL. È possibile modificarli facilmente, pianificarli, serializzare l'esecuzione o avviare molti in parallelo con un set completo di API. 
- Si tratta della base per la programmazione con le C# nuove estensioni del linguaggio asincrono. 

Il pool di thread aumenterà lentamente il numero di thread in base alle esigenze, in base al numero di core della CPU disponibili nel sistema, al carico del sistema e all'applicazione. È possibile usare questo pool di thread richiamando i metodi in `System.Threading.ThreadPool` o usando il `System.Threading.Tasks.TaskScheduler` predefinito (parte dei *Framework paralleli*).

In genere, gli sviluppatori usano i thread quando devono creare applicazioni reattive e non vogliono bloccare il ciclo di esecuzione principale dell'interfaccia utente.

 <a name="Developing_Responsive_Applications" />

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

 <a name="Threading_and_Garbage_Collection" />

## <a name="threading-and-garbage-collection"></a>Threading e Garbage Collection

Nel corso dell'esecuzione, il runtime di Objective-C creerà e rilascerà oggetti. Se gli oggetti sono contrassegnati per la "versione automatica", il runtime di Objective-C rilascerà tali oggetti all'`NSAutoReleasePool`corrente del thread. Xamarin.iOS crea un pool di `NSAutoRelease` per ogni thread dal `System.Threading.ThreadPool` e per il thread principale. Questa operazione per estensione copre tutti i thread creati usando il TaskScheduler predefinito in System. Threading. Tasks.

Se si creano thread personalizzati usando `System.Threading` è necessario fornire il proprio pool `NSAutoRelease` per evitare la perdita di dati. A tale scopo, è sufficiente eseguire il wrapping del thread nel seguente codice:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Nota: dal momento che Xamarin.iOS 5,2 non è necessario fornire il proprio `NSAutoReleasePool` più come uno verrà fornito automaticamente.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo del thread dell'interfaccia utente](~/ios/user-interface/ios-ui/ui-thread.md)
