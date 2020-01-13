---
title: Barra di spostamento Xamarin.Android
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/01/2017
ms.openlocfilehash: f28b9f19e901d75c432dfecbfec8a63588df3d70
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029223"
---
# <a name="xamarinandroid-navigation-bar"></a>Barra di spostamento Xamarin.Android

Android 4 ha introdotto una nuova funzionalità dell'interfaccia utente di sistema denominata *barra di navigazione*, che fornisce controlli di navigazione nei dispositivi che non includono i pulsanti hardware per **Home**, **back**e **menu**.
Lo screenshot seguente mostra la barra di navigazione da un dispositivo Nexus Prime:

 [![esempio di una barra di spostamento Android](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

Sono disponibili diversi nuovi flag che controllano la visibilità della barra di navigazione e dei relativi controlli, nonché la visibilità della barra di sistema introdotta in Android 3. I flag sono definiti nella classe `Android.View.View` e sono elencati di seguito:

- `SystemUiFlagVisible` &ndash; rende visibile la barra di navigazione. 
- `SystemUiFlagLowProfile` &ndash; offusca i controlli nella barra di spostamento. 
- `SystemUiFlagHideNavigation` &ndash; nasconde la barra di spostamento. 

Questi flag possono essere applicati a qualsiasi visualizzazione nella gerarchia di visualizzazione impostando la proprietà `SystemUiVisibility`. Se questa proprietà è impostata su più visualizzazioni, il sistema le combina con un'operazione o e le applica purché la finestra in cui sono impostati i flag mantenga lo stato attivo. Quando si rimuove una vista, verranno rimossi anche tutti i flag impostati.

Nell'esempio seguente viene illustrata un'applicazione semplice in cui fare clic su uno dei pulsanti per modificare la `SystemUiVisibility`:

 [![screenshot che dimostrano la visibilità, il profilo minimo e SystemUiVisibility nascosti](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

Il codice per modificare il `SystemUiVisibility` imposta la proprietà in un `TextView` dal gestore dell'evento Click di ciascun pulsante, come illustrato di seguito:

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

Inoltre, una modifica di `SystemUiVisibility` genera un evento di `SystemUiVisibilityChange`. Analogamente all'impostazione della proprietà `SystemUiVisibility`, un gestore per l'evento `SystemUiVisibilityChange` può essere registrato per qualsiasi visualizzazione nella gerarchia. Il codice seguente, ad esempio, usa l'istanza di `TextView` per eseguire la registrazione per l'evento:

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```

## <a name="related-links"></a>Collegamenti correlati

- [SystemUIVisibilityDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/systemuivisibilitydemo)
- [Introduzione a Ice Cream Sandwich](https://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
