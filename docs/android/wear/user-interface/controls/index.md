---
title: Controlli di usura Android
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: db240fa91c7eb99bd628fbb04c7c9352fb615e83
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119954"
---
# <a name="android-wear-controls"></a>Controlli di usura Android

Le app Android Wear possono usare molti degli stessi controlli già in uso per le normali app Android, `Button`tra `TextView`cui, e image drawables. È inoltre possibile `ScrollView`utilizzare `LinearLayout`i controlli `RelativateLayout` di layout, inclusi, e.

Questa pagina si collega ai controlli specifici di Android-Wear dalla [libreria di interfaccia utente indossabile](https://developer.android.com/training/wearables/apps/layouts.html#UiLibrary) disponibile nei progetti Novell tramite il pacchetto NuGet di [supporto indossabile](https://www.nuget.org/packages/Xamarin.Android.Wear/) . Questi controlli includono quanto segue:

- **GridViewPager** Creare un'interfaccia di navigazione bidimensionale in cui l'utente scorre verso il basso per eseguire una selezione (per altre informazioni, vedere [GridViewPager):](~/android/wear/user-interface/controls/gridviewpager.md) &ndash;

    ![Schermata di esempio di un GridViewPager](images/gridviewpager.png)

Altri importanti controlli per le app Wear includono:

- `BoxInsetLayout`(vedere [uso delle dimensioni dello schermo](~/android/wear/screen-sizes.md)),

- `WatchViewStub`(vedere [uso delle dimensioni dello schermo](~/android/wear/screen-sizes.md)),

- `CardFrame`(vedere la pagina relativa alla [creazione di schede Android](https://developer.android.com/training/wearables/ui/cards.html))

- `CardScrollView`(vedere la pagina relativa alla [creazione di schede Android](https://developer.android.com/training/wearables/ui/cards.html))

- `WearableListView`vedere gli [elenchi di creazione di Android](https://developer.android.com/training/wearables/ui/lists.html).


## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Android. support. Wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
