---
title: Layout a schede
description: Panoramica dei layout a schede in Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
ms.openlocfilehash: 5f67ec30ce04993701634387f7c2023a0f92004f
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522353"
---
# <a name="tabbed-layouts"></a>Layout a schede


## <a name="overview"></a>Panoramica

Le schede sono uno dei più diffusi modelli di interfaccia utente nelle applicazioni per dispositivi mobili, grazie alla semplicità e all'usabilità. Forniscono un modo semplice e coerente per spostarsi tra le varie schermate in un'applicazione. Android include diverse API per le interfacce a schede: 

- **ActionBar** &ndash; Questo è parte di un nuovo set di API introdotto in Android 3,0 (livello API 11) con l'obiettivo di fornire un'interfaccia coerente di spostamento e visualizzazione. È stato eseguito il porting in Android 2,2 (livello API 8) con la [libreria di supporto Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

- **PagerTabStrip** Indica le pagine correnti, successive e precedenti di un oggetto `ViewPager`. &ndash; `ViewPager`è disponibile solo tramite la [libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Per ulteriori informazioni su `PagerTabStrip`, vedere [viewpager](~/android/user-interface/controls/view-pager/index.md).

- **Barra degli strumenti** è un componente della barra delle azioni più recente e flessibile `ActionBar`che sostituisce. &ndash; `Toolbar` `Toolbar`è disponibile in Android 5,0 Lollipop o versioni successive ed è disponibile anche per le versioni precedenti di Android tramite il pacchetto NuGet della [libreria di supporto Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) . 
    `Toolbar`è attualmente il componente della barra delle azioni consigliato da usare nelle app per Android.
    Per ulteriori informazioni, vedere [barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Collegamenti correlati

- [Progettazione materiale-tabulazioni](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Libreria di supporto Android V7 AppCompat pacchetto NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [libreria AppCompat V7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
