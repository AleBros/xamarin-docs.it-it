---
title: Menu PopUp
description: Come aggiungere un menu popup che è ancorato a una visualizzazione specifica.
ms.prod: xamarin
ms.assetid: 1C58E12B-4634-4691-BF59-D5A3F6B0E6F7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: a5370cfb8a5c4950b361e5f58b253c63f4f1e240
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029172"
---
# <a name="xamarinandroid-popup-menu"></a>Menu di scelta rapida Novell. Android

Il [PopupMenu](xref:Android.Widget.PopupMenu) , detto anche _menu di scelta rapida_, è un menu che è ancorato a una visualizzazione specifica. Nell'esempio seguente, una singola attività contiene un pulsante. Quando l'utente tocca il pulsante, viene visualizzato un menu popup a tre elementi:

[![esempio di un'app con un pulsante e un menu a comparsa a tre elementi](popup-menu-images/01-app-example-sml.png)](popup-menu-images/01-app-example.png#lightbox)

## <a name="creating-a-popup-menu"></a>Creazione di un menu popup

Il primo passaggio consiste nel creare un file di risorse di menu per il menu e inserirlo in **risorse/menu**. Il codice XML seguente, ad esempio, è il codice per il menu di tre elementi visualizzato nella schermata precedente, **Resources/menu/popup_menu. XML**:

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

Successivamente, creare un'istanza di `PopupMenu` e ancorarla alla relativa visualizzazione. Quando si crea un'istanza di `PopupMenu`, viene passato al relativo costruttore un riferimento al `Context`, nonché la visualizzazione a cui il menu verrà collegato. Di conseguenza, il menu di scelta rapida è ancorato a questa visualizzazione durante la relativa costruzione.

Nell'esempio seguente, il `PopupMenu` viene creato nel gestore dell'evento click per il pulsante, denominato `showPopupMenu`. Questo pulsante è anche la visualizzazione a cui è ancorata la `PopupMenu`, come illustrato nell'esempio di codice seguente:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
};
```

Infine, il menu di scelta rapida *deve essere* ingrandito con la risorsa di menu creata in precedenza. Nell'esempio seguente viene aggiunta la chiamata al metodo [Inflate](xref:Android.Views.LayoutInflater.Inflate*) del menu e viene chiamato il metodo [show](xref:Android.Widget.PopupMenu.Show) per visualizzarlo:

```csharp
showPopupMenu.Click += (s, arg) => {
    PopupMenu menu = new PopupMenu (this, showPopupMenu);
    menu.Inflate (Resource.Menu.popup_menu);
    menu.Show ();
};
```

## <a name="handling-menu-events"></a>Gestione degli eventi di menu

Quando l'utente seleziona una voce di menu, viene generato l'evento Click [MenuItemClick](xref:Android.Widget.PopupMenu.MenuItemClick) e il menu viene eliminato. Toccare un punto qualsiasi all'esterno del menu lo chiuderà semplicemente. In entrambi i casi, quando il menu viene eliminato, viene generato il relativo [DismissEvent](xref:Android.Widget.PopupMenu.Dismiss) . Il codice seguente aggiunge gestori eventi per gli eventi `MenuItemClick` e `DismissEvent`:

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
