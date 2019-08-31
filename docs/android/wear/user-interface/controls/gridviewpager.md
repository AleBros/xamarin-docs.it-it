---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 5bdff0aee39375d8de296849056660f69a7d907f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198533"
---
# <a name="gridviewpager"></a>GridViewPager

L'esempio [GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager) illustra come implementare il modello di navigazione di selezione 2D per Android Wear.

![Schermata di esempio di GridViewPager in una visualizzazione quadrata](gridviewpager-images/gridviewpager.png)

Per prima cosa, aggiungere il pacchetto NuGet [Novell Android Wear support](https://www.nuget.org/packages/Xamarin.Android.Wear/) al progetto.

Il codice XML del layout è simile al seguente:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Creazione di un[`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(o sottoclasse come[`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
per fornire le visualizzazioni da visualizzare quando l'utente si sposta.

Nell' [adapter di esempio](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) viene illustrato come implementare i metodi necessari, incluse le `RowCount`sostituzioni `GetBackground`per, `GetColumnCount`, e`GetFragment`

Collegare la scheda come illustrato:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Collegamenti correlati

- [Documento di selezione 2D di Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [documentazione di Android. support. Wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)
