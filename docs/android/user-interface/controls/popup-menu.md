---
title: Menu di scelta rapida
ms.topic: article
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/18/2017
ms.openlocfilehash: f976d798ae1b1279fc8f82d3cf1d738bb2c93911
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="popup-menu"></a>Menu di scelta rapida

La `PopupMenu` classe aggiunge il supporto per visualizzare il menu di scelta rapida associati a una vista specifica. La figura seguente mostra un menu di scelta rapida visualizzato da un pulsante, il secondo elemento evidenziato solo quando viene selezionata:

 [![Esempio di un PopopMenu con tre tre elementi](popup-menu-images/20-popupmenu.png)](popup-menu-images/20-popupmenu.png#lightbox)

Android 4 aggiunte un paio di nuove funzionalità per `PopupMenu` che rendere un po' più semplice da usare, vale a dire:

-   **Inflate** &ndash; ingrandimento il metodo è ora disponibile direttamente sulla classe PopupMenu.
-   **DismissEvent** &ndash; PopupMenu la classe ha ora un DismissEvent.

Esaminiamo questi miglioramenti. In questo esempio è disponibile una singola attività che contiene un pulsante. Quando l'utente fa clic sul pulsante, viene visualizzato un menu popup come illustrato di seguito:

 [![Esempio di app in esecuzione in un emulatore con pulsante e menu a comparsa 3-elemento](popup-menu-images/06-popupmenu.png)](popup-menu-images/06-popupmenu.png#lightbox)


## <a name="creating-a-popup-menu"></a>Creazione di un Menu di scelta rapida

Quando si crea un'istanza di `PopupMenu`, è necessario passare il relativo costruttore un riferimento al `Context`, nonché la vista a cui è associato il menu. In questo caso, si crea il `PopupMenu` nel gestore dell'evento click per il pulsante, denominato `showPopupMenu`.
Questo pulsante è anche la vista a cui è possibile collegare il `PopupMenu`, come illustrato nel codice seguente:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
}
```

In Android 3, il codice di ingrandimento menu da una risorsa XML necessario ottenere innanzitutto un riferimento a un `MenuInflator`e quindi chiamare il relativo `Inflate` metodo con l'ID di risorsa del codice XML che contiene il menu e l'istanza di menu di ingrandimento in. Questo approccio funziona comunque in Android 4 e versioni successive come il codice seguente viene illustrato:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.MenuInflater.Inflate (Resource.Menu.popup_menu, menu.Menu);
};
```

A partire da Android 4, tuttavia, è ora possibile chiamare `Inflate` direttamente nell'istanza del `PopupMenu`. In questo modo il codice più conciso, come illustrato di seguito:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

Nel codice precedente, dopo il menu eccessi nella misurazione è sufficiente chiamare `menu.Show` per la visualizzazione sullo schermo.


## <a name="handling-menu-events"></a>Gestione degli eventi di Menu

Quando l'utente seleziona una voce di menu, il `MenuItemClick` verrà generato l'evento e il menu verrà chiusa. Toccando un punto qualsiasi all'esterno del menu verrà semplicemente ignorarla. In entrambi i casi, a partire da Android 4, quando il menu viene chiuso, il relativo `DismissEvent` , verrà generato. Il codice seguente aggiunge gestori eventi per entrambi i `MenuItemClick` e `DismissEvent` eventi:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);

    menu.MenuItemClick += (s1, arg1) => {
        Console.WriteLine ("{0} selected", arg1.Item.TitleFormatted);
    };

    menu.DismissEvent += (s2, arg2) => {
        Console.WriteLine ("menu dismissed");
    };
            menu.Show ();
};
```



## <a name="related-links"></a>Collegamenti correlati

- [PopupMenuDemo (esempio)](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
