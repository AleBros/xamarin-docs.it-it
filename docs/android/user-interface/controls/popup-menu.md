---
title: Menu di scelta rapida
description: Come aggiungere un menu di scelta rapida che è ancorata in una visualizzazione particolare.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 1e74c8b7745936f6e9a8890fd26acafe2f2fb6d5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288654"
---
# <a name="popup-menu"></a>Menu di scelta rapida

Il [PopupMenu](https://developer.xamarin.com/api/type/Android.Widget.PopupMenu/) (detto anche una _dal menu di scelta rapida_) è un menu di cui è ancorato a una particolare visualizzazione. Nell'esempio seguente, una singola attività contiene un pulsante. Quando l'utente tocca il pulsante, viene visualizzato un menu popup tre voci:

[![Esempio di un'app con un pulsante e tre voci di menu di scelta rapida](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>Creazione di un Menu Popup

Il primo passaggio consiste nel creare un file di risorse di menu per il menu di scelta e inserirla **risorse/menu**. Ad esempio, il codice XML seguente è il codice visualizzato nello screenshot precedente, il menu di tre voci **Resources/menu/popup_menu.xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/item1"
          android:title="item 1" />
    <item android:id="@+id/item1"
          android:title="item 2" />
    <item android:id="@+id/item1"
          android:title="item 3" />
</menu>
```

Successivamente, creare un'istanza di `PopupMenu` e ancorata alla relativa visualizzazione. Quando si crea un'istanza di `PopupMenu`, si passa il relativo costruttore un riferimento al `Context` , nonché la vista a cui verrà associato il menu di scelta. Di conseguenza, il menu di scelta rapida è agganciato a questa visualizzazione durante la costruzione.

Nell'esempio seguente, il `PopupMenu` viene creato nel gestore eventi click del pulsante (che ha un nome `showPopupMenu`). Questo pulsante è anche la vista a cui il `PopupMenu` è ancorata, come illustrato nell'esempio di codice seguente:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Infine, è necessario il menu di scelta rapida *gonfiato* con la risorsa di menu che è stata creata in precedenza. Nell'esempio seguente, la chiamata al menu [Inflate](https://developer.xamarin.com/api/member/Android.Views.LayoutInflater.Inflate/p/System.Int32/Android.Views.ViewGroup/) metodo viene aggiunto e i relativi [mostrano](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Show%28%29/) viene chiamato per consentire la visualizzazione:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>Gestione degli eventi di Menu

Quando l'utente seleziona una voce di menu, il [MenuItemClick](https://developer.xamarin.com/api/event/Android.Widget.PopupMenu.MenuItemClick/) fare clic su verrà generato l'evento e il menu di scelta verrà chiusa. Toccando un punto qualsiasi all'esterno del menu verrà semplicemente ignorarlo. In entrambi i casi, quando viene chiuso il menu di scelta, relativi [DismissEvent](https://developer.xamarin.com/api/member/Android.Widget.PopupMenu.Dismiss%28%29/) , verrà generato. Il codice seguente aggiunge gestori eventi per entrambi i `MenuItemClick` e `DismissEvent` eventi:

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
