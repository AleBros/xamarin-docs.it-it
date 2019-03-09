---
title: Layout a schede
description: Una panoramica di layout a schede in Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
---

# <a name="tabbed-layouts"></a>Layout a schede


## <a name="overview"></a>Panoramica

Le schede sono un modello di interfaccia utente comuni nelle applicazioni per dispositivi mobili grazie a livello di usabilità e semplicità. Forniscono un modo semplice e coerente per spostarsi tra le diverse schermate in un'applicazione. Android ha diverse API per le interfacce a schede: 

-   **ActionBar** &ndash; ciò fa parte di un nuovo set di API che è stato introdotto in Android 3.0 (API livello 11) con l'obiettivo di fornire un coerenti con l'interfaccia di cambio di visualizzazione e navigazione. È stato portato nuovamente per Android 2.2 (livello API 8) con i [libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica le pagine corrente, successiva e precedente di un `ViewPager`. `ViewPager` è disponibile solo tramite [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Per altre informazioni sulle `PagerTabStrip`, vedere [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Sulla barra degli strumenti** &ndash; `Toolbar` è un componente barra di azione più recente e più flessibile che sostituisce `ActionBar`. `Toolbar` è disponibile in Android 5.0 Lollipop o versioni successive, ed è anche disponibile per le versioni precedenti di Android tramite il [libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto NuGet. 
    `Toolbar` è attualmente il componente barra di azione consigliata da usare nelle App Android.
    Per altre informazioni, vedere [sulla barra degli strumenti](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Collegamenti correlati

- [Material Design - schede](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacchetto NuGet AppCompat v7 della libreria di supporto per Android](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [libreria appcompat V7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
