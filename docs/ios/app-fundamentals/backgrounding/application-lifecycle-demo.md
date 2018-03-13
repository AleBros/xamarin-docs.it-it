---
title: Demo di ciclo di vita dell'applicazione
ms.topic: article
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3d68b1e38ecb5b1833b818dd2a9fb7a5c84f9206
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="application-lifecycle-demo"></a>Demo di ciclo di vita dell'applicazione

In questa sezione verrà per un'applicazione che illustra i quattro stati dell'applicazione e il ruolo di esaminare il `AppDelegate` metodi notifica l'applicazione di quando vengono modificati alcune stati. L'applicazione verrà stampato gli aggiornamenti nella console ogni volta che l'applicazione modifica stato:

 [![](application-lifecycle-demo-images/image3.png "L'app di esempio")](application-lifecycle-demo-images/image3.png#lightbox)

 [![](application-lifecycle-demo-images/image4.png "L'app verrà stampato gli aggiornamenti nella console ogni volta che l'applicazione modifica stato")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Procedura dettagliata


  1. Aprire il _ciclo di vita_ nel progetto di _LifecycleDemo_ soluzione.
  1. Aprire la `AppDelegate` classe. Si noti che è stato aggiunto l'accesso ai metodi del ciclo di vita per farci sapere quando l'applicazione è stato modificato:

            ```chsarp
                public override void OnActivated(UIApplication application)
                {
                    Console.WriteLine("OnActivated called, App is active.");
                }
                public override void WillEnterForeground(UIApplication application)
                {
                    Console.WriteLine("App will enter foreground");
                }
                public override void OnResignActivation(UIApplication application)
                {
                    Console.WriteLine("OnResignActivation called, App moving to inactive state.");
                }
                public override void DidEnterBackground(UIApplication application)
                {
                    Console.WriteLine("App entering background state.");
                }
                // not guaranteed that this will run
                public override void WillTerminate(UIApplication application)
                {
                    Console.WriteLine("App is terminating.");
                }
            ```

  1. Avviare l'applicazione nel simulatore o sul dispositivo. `OnActivated` verrà chiamato quando viene avviata l'app. L'applicazione è ora nel _Active_ stato.
  1. Fare clic sul pulsante Home nel simulatore o nel dispositivo per portare l'applicazione in background. `OnResignActivation` e `DidEnterBackground` verrà chiamato il passaggio dell'app da `Active` a `Inactive` e nel `Backgrounded` dello stato. Poiché non è stato dato l'applicazione di qualsiasi codice da eseguire in background, l'applicazione è considerata _Suspended_ in memoria.
  1. Tornare all'app di riportarlo in primo piano. `WillEnterForeground` e `OnActivated` sia sarà:

        ![](application-lifecycle-demo-images/image4.png "Modifiche di stato stampato nella console")

    Si noti che la riga di codice seguente è stato aggiunto a questo controller di visualizzazione per informare che l'applicazione ha inserito il primo piano dallo sfondo:

        ```csharp
            UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
                    label.Text = "Welcome back!";
                });
        ```

1. Premere il **Home** pulsante per inserire l'applicazione in background. Quindi, toccare due volte il **Home** pulsante per visualizzare la selezione del tipo di applicazione:
    
    ![](application-lifecycle-demo-images/app-switcher-.png "La selezione del tipo di applicazione")
  
1. Individuare l'applicazione nella selezione di App e scorrere rapidamente per rimuoverlo:
    
    ![](application-lifecycle-demo-images/app-switcher-swipe-.png "Scorrere rapidamente per rimuovere un'app in esecuzione") 
    
iOS terminerà l'applicazione. Tenere presente che `WillTerminate` non viene chiamato perché ci stiamo chiusura di un'applicazione che è _Suspended_ in background.

Ora che sappiamo iOS applicazione stati e transizioni, prenderemo in esamina le diverse opzioni disponibili per backgrounding in iOS.



## <a name="related-links"></a>Collegamenti correlati

- [LifecycleDemo(Part2) (sample)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
