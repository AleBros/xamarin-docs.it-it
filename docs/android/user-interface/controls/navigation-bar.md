---
title: Barra di spostamento Novell. Android
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: c017ab96bdd78a41b31a3305021c5bc1f8e472d0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644918"
---
# <a name="xamarinandroid-navigation-bar"></a>Barra di spostamento Novell. Android

Android 4 ha introdotto una nuova funzionalità dell'interfaccia utente di sistema denominata *barra di navigazione*, che fornisce controlli di navigazione nei dispositivi che non includono i pulsanti hardware per **Home**, **back**e **menu**.
Lo screenshot seguente mostra la barra di navigazione da un dispositivo Nexus Prime:

 [![Esempio di una barra di spostamento Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Sono disponibili diversi nuovi flag che controllano la visibilità della barra di navigazione e dei relativi controlli, nonché la visibilità della barra di sistema introdotta in Android 3. I flag sono definiti nella `Android.View.View` classe e sono elencati di seguito:

-   `SystemUiFlagVisible`&ndash; Rende visibile la barra di navigazione. 
-   `SystemUiFlagLowProfile`&ndash; Offusca i controlli nella barra di spostamento. 
-   `SystemUiFlagHideNavigation`&ndash; Nasconde la barra di navigazione. 


Questi flag possono essere applicati a qualsiasi visualizzazione nella gerarchia di visualizzazione impostando la `SystemUiVisibility` proprietà. Se questa proprietà è impostata su più visualizzazioni, il sistema le combina con un'operazione o e le applica purché la finestra in cui sono impostati i flag mantenga lo stato attivo. Quando si rimuove una vista, verranno rimossi anche tutti i flag impostati.

Nell'esempio seguente viene illustrata un'applicazione semplice in cui fare clic su uno `SystemUiVisibility`dei pulsanti per modificare:

 [![Schermate che dimostrano la visibilità, il profilo basso e SystemUiVisibility nascosti](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

Il codice per modificare `SystemUiVisibility` imposta la proprietà su un `TextView` gestore dell'evento click del pulsante, come illustrato di seguito:

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

Inoltre, una `SystemUiVisibility` modifica genera un `SystemUiVisibilityChange` evento. Analogamente all'impostazione `SystemUiVisibility` della proprietà, è possibile registrare `SystemUiVisibilityChange` un gestore per l'evento per qualsiasi visualizzazione nella gerarchia. Il codice seguente, ad esempio, USA `TextView` l'istanza di per eseguire la registrazione per l'evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>Collegamenti correlati

- [SystemUIVisibilityDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/systemuivisibilitydemo)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
