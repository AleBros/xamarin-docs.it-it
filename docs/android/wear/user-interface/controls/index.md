---
title: Controlli di Android Wear
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f13b716e6ead8db8e0adafd180790c67bb82a6bc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112039"
---
# <a name="android-wear-controls"></a>Controlli di Android Wear

Le app Android Wear possono usare molti degli stessi controlli già in uso per le app Android regolari, compresi `Button`, `TextView`, un'immagine drawable. Controlli di layout compresi `ScrollView`, `LinearLayout`, e `RelativateLayout` può anche essere usato.

Questa pagina si collega ai controlli Android Wear specifici dal [indossabile libreria dell'interfaccia utente](https://developer.android.com/training/wearables/apps/layouts.html#UiLibrary) disponibili nei progetti Xamarin tramite il [supporto indossabile](http://www.nuget.org/packages/Xamarin.Android.Wear/) pacchetto NuGet. Questi controlli includono quanto segue:

-   **GridViewPager** &ndash; creare un'interfaccia di navigazione bidimensionale in cui l'utente scorre verso il basso, quindi tra per effettuare una selezione (per altre informazioni, vedere [GridViewPager](~/android/wear/user-interface/controls/gridviewpager.md)):

    ![Screenshot di esempio di un GridViewPager](images/gridviewpager.png)

Altri controlli per le app Wear importanti includono:

* `BoxInsetLayout` (vedere [funziona con le dimensioni dello schermo](~/android/wear/screen-sizes.md)),

* `WatchViewStub` (vedere [funziona con le dimensioni dello schermo](~/android/wear/screen-sizes.md)),

* `CardFrame` (vedere [schede di creazione di Android](https://developer.android.com/training/wearables/ui/cards.html)),

* `CardScrollView` (vedere [schede di creazione di Android](https://developer.android.com/training/wearables/ui/cards.html)),

* `WearableListView` (vedere [Android Crea elenchi](https://developer.android.com/training/wearables/ui/lists.html)).


## <a name="related-links"></a>Collegamenti correlati

- [Android.Support.Wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
