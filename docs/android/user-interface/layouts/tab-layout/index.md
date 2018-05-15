---
title: Layout a schede
description: Una panoramica di layout a schede in Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/08/2017
ms.openlocfilehash: 53ed5f91583d43839e96388194aea8c0d6ac5315
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="tabbed-layouts"></a>Layout a schede


## <a name="overview"></a>Panoramica

Le schede sono un modello di interfaccia utente più comunemente usati in applicazioni per dispositivi mobili a causa delle loro semplicità e usabilità. Forniscono un modo semplice e coerenza per spostarsi tra le schermate diverse in un'applicazione. Android ha diverse API per interfacce a schede: 

-   **ActionBar** &ndash; questo fa parte di un nuovo set di API che è stata introdotta in Android 3.0 (livello API 11) con l'obiettivo di fornire un coerente interfaccia cambio di visualizzazione e navigazione. È stata portata nuovamente per Android 2.2 (livello API 8) con il [libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica le pagine corrente, successiva e precedente di un `ViewPager`. `ViewPager` è disponibile solo tramite [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Per ulteriori informazioni su `PagerTabStrip`, vedere [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra degli strumenti** &ndash; `Toolbar` è un componente barra azione più recenti e più flessibile che sostituisce `ActionBar`. `Toolbar` è disponibile in Android simbolo 5.0 o versioni successive ed è anche disponibile per le versioni precedenti di Android tramite il [libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto NuGet. 
    `Toolbar` è attualmente il componente della barra azione consigliata da usare in App per Android.
    Per ulteriori informazioni, vedere [barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Collegamenti correlati

- [Progettazione materiale - schede](https://material.io/guidelines/components/tabs.html)- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacchetto NuGet di supporto Android libreria v7 delle applicazioni](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [libreria delle applicazioni V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
