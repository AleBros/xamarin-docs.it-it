---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 3a0b1ec9359b1c6067c253b4d04126dbdd726cc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763436"
---
# <a name="gridviewpager"></a>GridViewPager

Il [GridViewPager](https://developer.xamarin.com/samples/GridViewPager/) esempio viene illustrato come implementare il modello di navigazione selezione 2D per Android con accenti.

![Schermata di esempio di GridViewPager su uno schermo quadrato](gridviewpager-images/gridviewpager.png)

Aggiungere prima il [supporto Xamarin Android accenti](http://www.nuget.org/packages/Xamarin.Android.Wear/) pacchetto NuGet al progetto.

Il layout XML è simile al seguente:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Creare un [ `GridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html) (o una sottoclasse, ad esempio [ `FragmentGridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html) per fornire visualizzazioni da visualizzare come l'utente si sposta.

Il [adapter di esempio](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) viene illustrato come implementare i metodi necessari, compresi gli override per `RowCount`, `GetColumnCount`, `GetBackground`, e `GetFragment`

Associare l'adapter come illustrato:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Collegamenti correlati

- [Doc selezione 2D di Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [documenti Android.support.wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (esempio)](https://developer.xamarin.com/samples/GridViewPager/)
