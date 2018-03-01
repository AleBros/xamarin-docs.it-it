---
title: GridViewPager
ms.topic: article
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: f156d802d807c4087331ca5796b046f8f5f2fa0d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
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
