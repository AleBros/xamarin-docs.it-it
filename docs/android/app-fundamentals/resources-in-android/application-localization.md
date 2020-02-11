---
title: Localizzazione delle applicazioni e risorse di tipo stringa
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: 7191c37e81da728dfda21da2eaecc95f7b58a401
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025085"
---
# <a name="application-localization-and-string-resources"></a>Localizzazione delle applicazioni e risorse di tipo stringa

La localizzazione delle applicazioni è l'azione di fornire risorse alternative per la destinazione di un'area o di impostazioni locali specifiche. È ad esempio possibile fornire stringhe di lingua localizzate per diversi paesi oppure modificare i colori o il layout in base alle impostazioni cultura specifiche. Android caricherà e userà le risorse appropriate per le impostazioni locali del dispositivo in fase di runtime senza alcuna modifica al codice sorgente.

Ad esempio, l'immagine seguente mostra la stessa applicazione in esecuzione in tre diverse impostazioni locali del dispositivo, ma il testo visualizzato in ogni pulsante è specifico per le impostazioni locali in base alle quali ogni dispositivo è impostato su:

[![esempi di tre impostazioni locali diverse](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

In questo esempio, il contenuto di un file di layout, **Main. aXML** , ha un aspetto simile al seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent"
   >
<Button  
   android:id="@+id/myButton"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
android:text="@string/hello"
   />
</LinearLayout>
```

Nell'esempio precedente, la stringa per il pulsante è stata caricata dalle risorse fornendo l'ID risorsa per la stringa:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Stringhe di risorse per tre lingue](application-localization-images/02-resource-strings-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Stringhe di risorse per tre lingue](application-localization-images/02-resource-strings-xs.png)

-----

## <a name="localizing-android-apps"></a>Localizzazione di app Android

Leggere l' [Introduzione alla localizzazione](~/cross-platform/app-fundamentals/localization.md) per suggerimenti e istruzioni sulla localizzazione di app per dispositivi mobili.

La Guida [localizzazione di app Android](~/android/app-fundamentals/localization.md) contiene esempi più specifici su come tradurre le stringhe e localizzare immagini con Xamarin.Android.

## <a name="related-links"></a>Collegamenti correlati

- [Localizzazione di app Android](~/android/app-fundamentals/localization.md)
- [Panoramica della localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
