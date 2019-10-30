---
title: Layout a schede
description: Panoramica dei layout a schede in Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/08/2017
ms.openlocfilehash: 4ca4200d0f9036ed76e20e3a1840303e7bb3b7e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028786"
---
# <a name="tabbed-layouts"></a>Layout a schede

## <a name="overview"></a>Panoramica

Le schede sono uno dei più diffusi modelli di interfaccia utente nelle applicazioni per dispositivi mobili, grazie alla semplicità e all'usabilità. Forniscono un modo semplice e coerente per spostarsi tra le varie schermate in un'applicazione. Android include diverse API per le interfacce a schede: 

- **ActionBar** &ndash; questo fa parte di un nuovo set di API introdotto in Android 3,0 (livello API 11) con lo scopo di fornire un'interfaccia coerente di spostamento e visualizzazione. È stato eseguito il porting in Android 2,2 (livello API 8) con la [libreria di supporto Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

- **PagerTabStrip** &ndash; indica le pagine correnti, successive e precedenti di un `ViewPager`. `ViewPager` è disponibile solo tramite la [libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Per ulteriori informazioni su `PagerTabStrip`, vedere [viewpager](~/android/user-interface/controls/view-pager/index.md).

- **Barra degli strumenti** &ndash; `Toolbar` è un componente della barra delle azioni più nuovo e flessibile che sostituisce `ActionBar`. `Toolbar` è disponibile in Android 5,0 Lollipop o versione successiva ed è disponibile anche per le versioni precedenti di Android tramite il pacchetto NuGet della [libreria di supporto Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) . 
    `Toolbar` è attualmente il componente della barra delle azioni consigliato da usare nelle app per Android.
    Per ulteriori informazioni, vedere [barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 

## <a name="related-links"></a>Collegamenti correlati

- [Progettazione materiale-tabulazioni](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Libreria di supporto Android V7 AppCompat pacchetto NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [libreria AppCompat V7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
