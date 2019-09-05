---
title: Demo del ciclo di vita delle applicazioni per Novell. iOS
description: Questo documento esamina diversi eventi del ciclo di vita gestiti dal delegato dell'app in un'applicazione iOS, dimostrando quando e come vengono gestiti questi eventi.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/17/2018
ms.openlocfilehash: d130d28b6cf0f15dab3a743a9a3fba144b75a67d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289428"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Demo del ciclo di vita delle applicazioni per Novell. iOS

Questo articolo e il [codice di esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo) illustrano i quattro Stati dell'applicazione in iOS e il `AppDelegate` ruolo dei metodi per notificare all'applicazione quando gli Stati vengono modificati. L'applicazione visualizzerà gli aggiornamenti alla console ogni volta che lo stato dell'app cambia:

[![](application-lifecycle-demo-images/image3-sml.png "App di esempio")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "L'app stampa gli aggiornamenti alla console ogni volta che lo stato dell'app viene modificato")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Procedura dettagliata

1. Aprire il progetto **Lifecycle** nella soluzione **LifecycleDemo** .
1. Aprire la `AppDelegate` classe. La registrazione è stata aggiunta ai metodi del ciclo di vita per indicare la modifica dello stato dell'applicazione:

    ```csharp
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

1. Avviare l'applicazione nel simulatore o nel dispositivo. `OnActivated`verrà chiamato all'avvio dell'app. L'applicazione è ora nello stato _attivo_ .
1. Premere il pulsante Home sul simulatore o sul dispositivo per portare l'applicazione in background. `OnResignActivation`e `DidEnterBackground` verranno chiamati durante le transizioni dell'app da `Active` a `Inactive` e nello `Backgrounded` stato. Poiché il codice dell'applicazione non è impostato per l'esecuzione in background, l'applicazione viene considerata _sospesa_ in memoria.
1. Tornare all'app per ripristinarlo in primo piano. `WillEnterForeground``OnActivated` verranno entrambi chiamati:

    ![](application-lifecycle-demo-images/image4.png "Modifiche di stato stampate nella console")

    La seguente riga di codice nel controller di visualizzazione viene eseguita quando l'applicazione è passata in primo piano dallo sfondo e modifica il testo visualizzato sullo schermo:

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Premere il pulsante **Home** per inserire l'applicazione in background. Quindi, toccare il pulsante **Home** per visualizzare il selettore di applicazione. In iPhone X scorrere rapidamente verso l'alto dalla parte inferiore della schermata:

    [Selezione ![applicazione] Selezione (application-lifecycle-demo-images/app-switcher-sml.png "applicazione")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Individuare l'applicazione nello strumento di selezione dell'app e scorrere verso l'alto per rimuoverla (in iOS 11, premere lungo finché le icone rosse non vengono visualizzate nell'angolo):

    [![Scorrere rapidamente verso l'alto per rimuovere un'app in esecuzione](application-lifecycle-demo-images/app-switcher-swipe-sml.png "Scorrere rapidamente verso l'alto per rimuovere un'app in esecuzione")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS terminerà l'applicazione. Si noti `WillTerminate` che non viene chiamato perché l'applicazione è già _sospesa_ in background.

## <a name="related-links"></a>Collegamenti correlati

- [LifecycleDemo (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo)
