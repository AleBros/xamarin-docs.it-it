---
title: Threading
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 6d178231cd45d3b251a26c47abd47bf22b6c2716
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="threading"></a>Threading

Il runtime di xamarin offre agli sviluppatori l'accesso a .NET API, entrambi in modo esplicito quando si utilizza thread di threading (`System.Threading.Thread, System.Threading.ThreadPool`) e in modo implicito quando si utilizzano i modelli di delegato asincrono o i metodi BeginXXX, nonché l'intervallo completo di API che supportano il Task Parallel Library.



Xamarin consiglia vivamente di utilizzare il [Task Parallel Library](http://msdn.microsoft.com/en-us/library/dd460717.aspx) (TPL) per la compilazione di applicazioni per diversi motivi:
-  L'utilità di pianificazione predefinita TPL delegherà l'esecuzione dell'attività al pool di thread, che a sua volta aumenterà in modo dinamico il numero di thread necessarie come processo ha luogo, si evita di uno scenario in cui troppi thread rimangano in competizione per tempo CPU. 
-  Risulta più semplice considerare le operazioni in termini di attività TPL. È possibile modificarli, pianificazione, l'esecuzione di serializzare o avvio in parallelo con un ampio set di API. 
-  È la base per la programmazione con le estensioni del linguaggio di nuovo c# asincrono. 


Il pool di thread lenta aumenterà il numero di thread come necessario in base al numero di core CPU disponibile nel sistema, il carico di sistema e le richieste dell'applicazione. È possibile utilizzare il pool di thread chiamando metodi in `System.Threading.ThreadPool` o utilizzando il valore predefinito `System.Threading.Tasks.TaskScheduler` (in parte il *Framework parallelo*).

In genere gli sviluppatori di utilizzano i thread quando è necessario creare applicazioni reattive e non si desidera bloccare l'interfaccia utente principale eseguire ciclo.

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>Sviluppo di applicazioni reattive

L'accesso agli elementi dell'interfaccia utente dovrebbe essere limitato allo stesso thread che esegue il ciclo principale per l'applicazione. Se si desidera apportare modifiche all'interfaccia utente principale da un thread, è necessario mettere in coda il codice utilizzando [NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/type/Foundation.NSObject/), come segue:

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

Il precedente richiama il codice all'interno di un delegato di nel contesto del thread principale, senza causare alcun race condition che potrebbe arrestarsi in modo anomalo dell'applicazione.

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>Threading e Garbage Collection

Nel corso di esecuzione, il runtime Objective-C creare e rilasciare gli oggetti. Se gli oggetti vengono contrassegnati per la "rilascio automatico" Objective-C runtime rilascerà tali oggetti corrente del thread di `NSAutoReleasePool`. Xamarin consente di creare uno `NSAutoRelease` pool per ogni thread di `System.Threading.ThreadPool` e per il thread principale. Dall'estensione sono inclusi tutti i thread creati utilizzando il valore predefinito TaskScheduler System.Threading.Tasks.

Se si creano i propri thread utilizzando `System.Threading` è necessario fornire si è proprietari `NSAutoRelease` pool per impedire che i dati dalla divulgazione. A tale scopo, è sufficiente eseguire il wrapping del thread nel seguente frammento di codice:

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

Nota: Poiché xamarin. IOS 5.2 non è di fornire una propria `NSAutoReleasePool` più uno verrà visualizzato automaticamente.


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo del thread dell'interfaccia utente](~/ios/user-interface/ios-ui/ui-thread.md)
