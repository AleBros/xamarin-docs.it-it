---
title: Avviare l'app map nativa da Xamarin.Forms
description: L'app native Maps in ogni piattaforma può essere avviata da un'applicazione Xamarin.Forms mediante la classe dell'utilità di avvio Xamarin.Essentials.
ms.prod: xamarin
ms.assetid: 5CF7CD67-3F20-4D80-B99E-D35A5FD1019A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: 54776d28bb75b152a6402e4d531d1baa4f724cba
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426202"
---
# <a name="launch-the-native-map-app-from-xamarinforms"></a>Avviare l'app map nativa da Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

L'app map nativa in ogni piattaforma può essere avviata da un'applicazione Xamarin.Forms dalla classe Xamarin.Essentials `Launcher`. Questa classe consente a un'applicazione di aprire un'altra app tramite il relativo schema URI personalizzato. La funzionalità di avvio può essere richiamata con il metodo `OpenAsync`, passando un argomento `string` o `Uri` che rappresenta lo schema URL personalizzato da aprire. Per altre informazioni su Xamarin.Essentials, vedere [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms).

> [!NOTE]
> Un'alternativa all'uso della classe `Launcher` Xamarin.Essentials consiste nell'usare la classe `Map`. Per altre informazioni, vedere [Xamarin.Essentials: Map](~/essentials/maps.md?context=xamarin/xamarin-forms).

L'app Maps in ogni piattaforma usa uno schema URI personalizzato univoco. Per informazioni sullo schema URI Maps in iOS, vedere [collegamenti Mappa](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html) in Developer.Apple.com. Per informazioni sullo schema URI di Maps in Android, vedere [Maps Developer Guide](https://developer.android.com/guide/components/intents-common.html#Maps) e [Google Maps Intent per Android](https://developers.google.com/maps/documentation/urls/android-intents) in Developers.Android.com. Per informazioni sullo schema URI Maps nella piattaforma UWP (Universal Windows Platform) (UWP), vedere [avviare l'app Windows Maps](/windows/uwp/launch-resume/launch-maps-app).

## <a name="launch-the-map-app-at-a-specific-location"></a>Avviare l'app map in una posizione specifica

È possibile aprire un percorso nell'app native Maps aggiungendo parametri di query appropriati allo schema URI personalizzato per ogni app map:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // open the maps app directly
    await Launcher.OpenAsync("geo:0,0?q=394+Pacific+Ave+San+Francisco+CA");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?where=394 Pacific Ave San Francisco CA");
}
```

Questo esempio di codice comporta l'avvio dell'app mappa nativa in ogni piattaforma, con la mappa centrata su un pin che rappresenta il percorso specificato:

[![Screenshot dell'app mappa nativa in iOS e Android](native-map-app-images/location.png "App mappa nativa")](native-map-app-images/location-large.png#lightbox "App mappa nativa")

## <a name="launch-the-map-app-with-directions"></a>Avviare l'app map con le direzioni

È possibile avviare l'app native Maps per visualizzare le direzioni, aggiungendo parametri di query appropriati allo schema URI personalizzato per ogni app map:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    // https://developer.apple.com/library/ios/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html
    await Launcher.OpenAsync("http://maps.apple.com/?daddr=San+Francisco,+CA&saddr=cupertino");
}
else if (Device.RuntimePlatform == Device.Android)
{
    // opens the 'task chooser' so the user can pick Maps, Chrome or other mapping app
    await Launcher.OpenAsync("http://maps.google.com/?daddr=San+Francisco,+CA&saddr=Mountain+View");
}
else if (Device.RuntimePlatform == Device.UWP)
{
    await Launcher.OpenAsync("bingmaps:?rtp=adr.394 Pacific Ave San Francisco CA~adr.One Microsoft Way Redmond WA 98052");
}
```

Questo esempio di codice comporta l'avvio dell'app mappa nativa in ogni piattaforma, con la mappa centrata su una route tra le posizioni specificate:

[![Screenshot della route dell'app mappa nativa, in iOS e Android](native-map-app-images/directions.png "Direzioni app mappa nativa")](native-map-app-images/directions-large.png#lightbox "Direzioni app mappa nativa")

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.Essentials](~/essentials/index.md?context=xamarin/xamarin-forms)
- [Collegamenti Mappa](https://developer.apple.com/library/archive/featuredarticles/iPhoneURLScheme_Reference/MapLinks/MapLinks.html)
- [Guida per sviluppatori Maps](https://developer.android.com/guide/components/intents-common.html#Maps)
- [Intent di Google Maps per Android](https://developers.google.com/maps/documentation/)
- [Avviare l'app Windows Maps](/windows/uwp/launch-resume/launch-maps-app)
