---
title: Il threading in xamarin. IOS
description: Questo documento descrive come usare le APIs System.Threading in un'applicazione xamarin. IOS. Vengono illustrati la Task Parallel Library, creazione di applicazioni reattive e operazioni di garbage collection.
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 7dbb0044f09d5bc00f2393eb647efba05a061c3f
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669544"
---
# <a name="threading-in-xamarinios"></a>Il threading in xamarin. IOS

Il runtime di xamarin. IOS consente agli sviluppatori di accedere a .NET API, entrambi in modo esplicito quando si usano i thread di threading (`System.Threading.Thread, System.Threading.ThreadPool`) e in modo implicito quando si usano i modelli di delegato asincrono o i metodi BeginXXX, nonché la gamma completa di API che supportano il Task Parallel Library.



Xamarin consiglia vivamente di utilizzare il [Task Parallel Library](https://msdn.microsoft.com/library/dd460717.aspx) (TPL) per la compilazione di applicazioni per diversi motivi:
-  L'utilità di pianificazione predefinita TPL delegherà l'esecuzione dell'attività al pool di thread, che a sua volta aumenterà in modo dinamico il numero di thread necessari come processo viene eseguita, evitando uno scenario in cui un numero eccessivo di thread finire in competizione per tempo di CPU. 
-  È più semplice considerare le operazioni in termini di attività TPL. È facilmente possibile modificarli, pianificarle, serializzare l'esecuzione o avviare molte in parallelo con una vasta gamma di API. 
-  È la base per la programmazione con le nuove estensioni del linguaggio di async c#. 


Il pool di thread lentamente aumenterà il numero di thread come necessario in base al numero di core CPU disponibili nel sistema, il carico di sistema e le esigenze dell'applicazione. È possibile usare questo pool di thread richiamando i metodi nel `System.Threading.ThreadPool` oppure usando il valore predefinito `System.Threading.Tasks.TaskScheduler` (fa parte del *Framework parallele*).

In genere gli sviluppatori di utilizzano i thread quando hanno bisogno per creare applicazioni reattive e non si desidera bloccare l'interfaccia utente principale eseguire ciclo.

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>Lo sviluppo di applicazioni reattive

Accesso a elementi dell'interfaccia utente deve essere limitata allo stesso thread che esegue il ciclo principale per l'applicazione. Se si desidera apportare modifiche all'interfaccia utente principale da un thread, è necessario accodare il codice usando [NSObject.InvokeOnMainThread](xref:Foundation.NSObject), simile al seguente:

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

Il codice precedente richiama il codice all'interno di delegato nel contesto del thread principale, senza causare alcun race condition che potrebbe potenzialmente di arresto anomalo dell'applicazione.

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>Threading e Garbage Collection

Nel corso dell'esecuzione, il runtime di Objective-C verrà creare e rilasciare gli oggetti. Se gli oggetti contrassegnati per la "auto-rilascio" il runtime di Objective-C rilascerà tali oggetti al corrente del thread di `NSAutoReleasePool`. Xamarin. IOS consente di creare uno `NSAutoRelease` per ogni thread dal pool di `System.Threading.ThreadPool` e per il thread principale. Tale estensione copre tutti i thread creati utilizzando l'oggetto TaskScheduler predefinito in Tasks.

Se si crea il proprio thread che usano `System.Threading` è necessario fornire possedute `NSAutoRelease` pool per evitare che i dati dalla divulgazione. A tale scopo, è semplicemente il wrapping del thread nel seguente frammento di codice:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Nota: Poiché xamarin. IOS 5.2 non è necessario fornire il proprio `NSAutoReleasePool` più come una riceveranno automaticamente per l'utente.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo del thread dell'interfaccia utente](~/ios/user-interface/ios-ui/ui-thread.md)
