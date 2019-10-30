---
title: Controlli di usura Android
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 54ef09af1da484305eecc2107d0245426d11a646
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030300"
---
# <a name="android-wear-controls"></a>Controlli di usura Android

Le app Android Wear possono usare molti degli stessi controlli già usati per le normali app Android, tra cui `Button`, `TextView`e image drawables. È inoltre possibile utilizzare i controlli di layout, inclusi `ScrollView`, `LinearLayout`e `RelativateLayout`.

Questa pagina si collega ai controlli specifici di Android-Wear dalla [libreria di interfaccia utente indossabile](https://developer.android.com/training/wearables/apps/layouts.html#UiLibrary) disponibile nei progetti Novell tramite il pacchetto NuGet di [supporto indossabile](https://www.nuget.org/packages/Xamarin.Android.Wear/) . Questi controlli includono quanto segue:

- **GridViewPager** &ndash; creare un'interfaccia di navigazione bidimensionale in cui l'utente scorre verso il basso per effettuare una selezione (per ulteriori informazioni, vedere [GridViewPager](~/android/wear/user-interface/controls/gridviewpager.md)):

    ![Schermata di esempio di un GridViewPager](images/gridviewpager.png)

Altri importanti controlli per le app Wear includono:

- `BoxInsetLayout` (vedere [utilizzo delle dimensioni dello schermo](~/android/wear/screen-sizes.md)),

- `WatchViewStub` (vedere [utilizzo delle dimensioni dello schermo](~/android/wear/screen-sizes.md)),

- `CardFrame` (vedere la pagina relativa alla [creazione di schede Android](https://developer.android.com/training/wearables/ui/cards.html))

- `CardScrollView` (vedere la pagina relativa alla [creazione di schede Android](https://developer.android.com/training/wearables/ui/cards.html))

- `WearableListView` (vedere gli [elenchi di creazione di Android](https://developer.android.com/training/wearables/ui/lists.html)).

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Android. support. Wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
