---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 1cb71fa2c73b9ab151555559b22def4be1cf5c73
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112767"
---
# <a name="gridviewpager"></a>GridViewPager

Il [GridViewPager](https://developer.xamarin.com/samples/GridViewPager/) esempio viene illustrato come implementare il modello di spostamento selezione 2D per Android Wear.

![Screenshot di esempio del GridViewPager su uno schermo quadrato](gridviewpager-images/gridviewpager.png)

Aggiungere prima la [supporto di Xamarin Android Wear](http://www.nuget.org/packages/Xamarin.Android.Wear/) pacchetto NuGet al progetto.

Il layout XML simile al seguente:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Creare un [`GridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(o una sottoclasse, ad esempio [`FragmentGridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
per fornire visualizzazioni da visualizzare come l'utente si sposta.

Il [adapter di esempio](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) illustra come implementare i metodi richiesti, incluso gli override per `RowCount`, `GetColumnCount`, `GetBackground`, e `GetFragment`

Collegare la scheda come illustrato:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Collegamenti correlati

- [Doc selezione 2D di Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [Android.support.wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (esempio)](https://developer.xamarin.com/samples/GridViewPager/)
