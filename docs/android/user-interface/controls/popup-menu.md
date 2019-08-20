---
title: Menu PopUp
description: Come aggiungere un menu popup che è ancorato a una visualizzazione specifica.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: 9b3e4177d6be5854e80952d091aa78787d9645bb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644928"
---
# <a name="xamarinandroid-popup-menu"></a>Menu di scelta rapida Novell. Android

Il [PopupMenu](xref:Android.Widget.PopupMenu) , detto anche _menu di scelta rapida_, è un menu che è ancorato a una visualizzazione specifica. Nell'esempio seguente, una singola attività contiene un pulsante. Quando l'utente tocca il pulsante, viene visualizzato un menu popup a tre elementi:

[![Esempio di un'app con un pulsante e un menu a comparsa a tre elementi](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)


## <a name="creating-a-popup-menu"></a>Creazione di un menu popup

Il primo passaggio consiste nel creare un file di risorse di menu per il menu e inserirlo in **risorse/menu**. Il codice XML seguente, ad esempio, è il codice per il menu di tre elementi visualizzato nella schermata precedente, Resources **/menu/popup_menu. XML**:

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

Successivamente, creare un'istanza di `PopupMenu` e ancorarla alla relativa visualizzazione. Quando si crea un'istanza di `PopupMenu`, si passa il relativo costruttore a un riferimento `Context` a e alla visualizzazione a cui il menu verrà collegato. Di conseguenza, il menu di scelta rapida è ancorato a questa visualizzazione durante la relativa costruzione.

Nell'esempio seguente, `PopupMenu` viene creato nel gestore dell'evento click per il pulsante (denominato `showPopupMenu`). Questo pulsante è anche la visualizzazione a cui è `PopupMenu` ancorato l'oggetto, come illustrato nell'esempio di codice seguente:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Infine, il menu di scelta rapida *deve essere* ingrandito con la risorsa di menu creata in precedenza. Nell'esempio seguente viene aggiunta la chiamata al metodo [Inflate](xref:Android.Views.LayoutInflater.Inflate*) del menu e viene chiamato il metodo [Show](xref:Android.Widget.PopupMenu.Show) per visualizzarlo:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```


## <a name="handling-menu-events"></a>Gestione degli eventi di menu

Quando l'utente seleziona una voce di menu, viene generato l'evento Click [MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) e il menu viene eliminato. Toccare un punto qualsiasi all'esterno del menu lo chiuderà semplicemente. In entrambi i casi, quando il menu viene eliminato, viene generato il relativo [DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) . Il codice seguente aggiunge i gestori eventi per gli `MenuItemClick` eventi e: `DismissEvent`

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

- [PopupMenuDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo)
