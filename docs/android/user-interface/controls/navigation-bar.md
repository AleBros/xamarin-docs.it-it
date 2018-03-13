---
title: Barra di spostamento
ms.topic: article
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/01/2017
ms.openlocfilehash: fe76c93afc149553e44b5e8fa29a21767becf5c5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="navigation-bar"></a>Barra di spostamento

Android 4 introdotta una nuova funzionalità interfaccia utente di sistema denominata un *barra di spostamento*, che fornisce i controlli per la navigazione nei dispositivi che non includono i pulsanti hardware **Home**, **indietro** , e **Menu**.
La schermata seguente mostra la barra di spostamento da un dispositivo Nexus primi:

 [![Esempio di una barra di spostamento Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Sono disponibili numerosi nuovi flag che controllano la visibilità della barra di spostamento e i relativi controlli, nonché la visibilità della barra di sistema che è stata introdotta in Android 3. I flag sono definiti nella `Android.View.View` classe e sono elencati di seguito:

-   `SystemUiFlagVisible` &ndash; Rende visibile la barra di spostamento. 
-   `SystemUiFlagLowProfile` &ndash; Dims controlli nella barra di spostamento. 
-   `SystemUiFlagHideNavigation` &ndash; Nasconde la barra di spostamento. 


Questi flag è applicabile a qualsiasi visualizzazione nella gerarchia di visualizzazione impostando la `SystemUiVisibility` proprietà. Se più visualizzazioni impostare questa proprietà, il sistema li combina con un'operazione di OR e li applica a condizione che la finestra in cui i flag sono impostati mantiene lo stato attivo. Quando si rimuove una vista, verranno rimossi anche i flag che è impostato.

L'esempio seguente mostra una semplice applicazione in cui viene modificato facendo clic su uno dei pulsanti di `SystemUiVisibility`:

 [![Screenshot che illustra Visible ridotto e SystemUiVisibility nascosto](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

Il codice per modificare il `SystemUiVisibility` imposta la proprietà su un `TextView` da ogni pulsante gestore dell'evento click come indicato di seguito:

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

Inoltre, un `SystemUiVisibility` modificare genera un `SystemUiVisibilityChange` evento. Come impostazione di `SystemUiVisibility` proprietà, un gestore per il `SystemUiVisibilityChange` evento può essere registrato per qualsiasi visualizzazione nella gerarchia. Ad esempio, il codice seguente viene utilizzato il `TextView` istanza da registrare per l'evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Collegamenti correlati

- [SystemUIVisibilityDemo (sample)](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
