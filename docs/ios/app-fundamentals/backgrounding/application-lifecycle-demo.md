---
title: Demo del ciclo di vita dell'applicazione per xamarin. IOS
description: Questo documento vengono esaminati vari eventi del ciclo di vita gestiti dal delegato dell'app in un'applicazione iOS, che illustra come e quando questi eventi sono gestiti.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/17/2018
ms.openlocfilehash: 3beb511c03b328ecea824bf89355d056df003f3e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102698"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Demo del ciclo di vita dell'applicazione per xamarin. IOS

Questo articolo e [esempi di codice](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/) vengono illustrati gli stati di quattro applicazioni in iOS e il ruolo del `AppDelegate` metodi che informa l'applicazione di quando vengono modificati alcune gli stati. Ogni volta che l'app viene modificato lo stato, l'applicazione stamperà gli aggiornamenti nella console:

[![](application-lifecycle-demo-images/image3-sml.png "L'app di esempio")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "L'app verrà stampato gli aggiornamenti nella console ogni volta che l'app modifica stato")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Procedura dettagliata

1. Aprire il **ciclo di vita** del progetto nel **LifecycleDemo** soluzione.
1. Aprire il `AppDelegate` classe. La registrazione è stata aggiunta ai metodi del ciclo di vita per indicare quando l'applicazione è stato modificato:

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

1. Avviare l'applicazione nel simulatore o sul dispositivo. `OnActivated` verrà chiamato quando l'app viene avviata. L'applicazione è ora nel _Active_ dello stato.
1. Fare clic sul pulsante Home nel simulatore o nel dispositivo per portare l'applicazione in background. `OnResignActivation` e `DidEnterBackground` verrà chiamato come le transizioni app dallo `Active` al `Inactive` e nel `Backgrounded` dello stato. Poiché non esiste alcun set di codice dell'applicazione per l'esecuzione in background, l'applicazione venga considerata _sospeso_ in memoria.
1. Passare all'App per riportarlo in primo piano. `WillEnterForeground` e `OnActivated` verranno entrambi chiamati:

    ![](application-lifecycle-demo-images/image4.png "Modifiche di stato stampato nella console")

    La seguente riga di codice nel controller di visualizzazione viene eseguita quando l'applicazione entrato in primo piano dallo sfondo e sostituisce il testo visualizzato sullo schermo:

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Premere il **Home** pulsante per inserire l'applicazione in background. Quindi, toccare due volte il **Home** pulsante per visualizzare la selezione dell'applicazione. In un iPhone X, scorrere verso il backup dalla parte inferiore della schermata:

    [![La selezione applicazione](application-lifecycle-demo-images/app-switcher-sml.png "del selettore di applicazioni")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Individuare l'applicazione nel cambio modalità per l'App e scorrere rapidamente verso l'iscrizione per rimuoverlo (in iOS 11, pressione prolungata fino a quando non vengono visualizzate le icone di colore rosso nell'angolo):

    [![Scorrere fino a rimuovere un'app in esecuzione](application-lifecycle-demo-images/app-switcher-swipe-sml.png "scorrere fino a rimuovere un'app in esecuzione")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

Per terminare l'applicazione iOS. Si noti che `WillTerminate` non viene chiamato perché l'applicazione è già _sospeso_ in background.

## <a name="related-links"></a>Collegamenti correlati

- [LifecycleDemo (esempio)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
