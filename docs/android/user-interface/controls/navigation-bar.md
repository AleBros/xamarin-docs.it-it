---
title: Barra di spostamento
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 9455cac81a0f9ea81e08cf63397e45c1698e1c1b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153639"
---
# <a name="navigation-bar"></a>Barra di spostamento

Android 4 è stato introdotto una nuova funzionalità dell'interfaccia utente di sistema denominata un *sulla barra di navigazione*, che fornisce i controlli di navigazione nei dispositivi che non includono i pulsanti hardware per **Home**, **indietro** , e **Menu**.
Lo screenshot seguente mostra la barra di spostamento da un dispositivo di primo Nexus:

 [![Esempio di una barra di spostamento in Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Sono disponibili numerosi nuovi flag che controllano la visibilità della barra di spostamento e i relativi controlli, nonché la visibilità della barra di sistema che è stato introdotto in Android 3. I flag sono definiti nel `Android.View.View` classe e sono elencate di seguito:

-   `SystemUiFlagVisible` &ndash; Rende visibile la barra di spostamento. 
-   `SystemUiFlagLowProfile` &ndash; Dims dei controlli nella barra di spostamento. 
-   `SystemUiFlagHideNavigation` &ndash; Nasconde la barra di spostamento. 


Questi flag possono essere applicati a qualsiasi visualizzazione nella gerarchia di visualizzazione con l'impostazione di `SystemUiVisibility` proprietà. Se più viste impostato questa proprietà, il sistema li combina con un'operazione di OR e li applica in modo fino a quando la finestra in cui sono impostati i flag mantiene lo stato attivo. Quando si rimuove una visualizzazione, verranno rimossi anche eventuali flag che è impostato.

L'esempio seguente illustra una semplice applicazione in cui viene modificato facendo clic su uno dei pulsanti di `SystemUiVisibility`:

 [![Screenshot che illustra Visible, basso profilo e SystemUiVisibility nascosti](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

Il codice per modificare la `SystemUiVisibility` imposta la proprietà su un `TextView` da ogni pulsante gestore dell'evento click come indicato di seguito:

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

Inoltre, un `SystemUiVisibility` modifica genera un `SystemUiVisibilityChange` evento. Come impostazione il `SystemUiVisibility` proprietà, un gestore per il `SystemUiVisibilityChange` evento può essere registrato per qualsiasi visualizzazione della gerarchia. Ad esempio, il codice seguente usa il `TextView` istanza da registrare per l'evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Collegamenti correlati

- [SystemUIVisibilityDemo (esempio)](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4.0](https://developer.android.com/sdk/android-4.0.html)
