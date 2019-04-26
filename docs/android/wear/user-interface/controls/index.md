---
title: Controlli di Android Wear
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9620362b48e9cf1db4c25e4470851fb518a8e0f3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285587"
---
# <a name="android-wear-controls"></a>Controlli di Android Wear

Le app Android Wear possono usare molti degli stessi controlli già in uso per le app Android regolari, compresi `Button`, `TextView`, un'immagine drawable. Controlli di layout compresi `ScrollView`, `LinearLayout`, e `RelativateLayout` può anche essere usato.

Questa pagina si collega ai controlli Android Wear specifici dal [indossabile libreria dell'interfaccia utente](https://developer.android.com/training/wearables/apps/layouts.html#UiLibrary) disponibili nei progetti Xamarin tramite il [supporto indossabile](https://www.nuget.org/packages/Xamarin.Android.Wear/) pacchetto NuGet. Questi controlli includono quanto segue:

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
