---
title: Localizzazione delle applicazioni e risorse di tipo stringa
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: d9d90e371199c8587d61199240523cf0a23f5efd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116524"
---
# <a name="application-localization-and-string-resources"></a>Localizzazione delle applicazioni e risorse di tipo stringa

Localizzazione delle applicazioni è l'atto di offrire risorse alternative per un'area specifica o le impostazioni locali di destinazione. Ad esempio, è possibile fornire le stringhe localizzate per paesi diversi, oppure è possibile modificare i colori o il layout per la corrispondenza le impostazioni cultura specifiche. Android caricherà e usare le risorse appropriate per le impostazioni locali del dispositivo in fase di runtime senza apportare modifiche al codice sorgente.

Ad esempio, l'immagine seguente mostra la stessa applicazione in esecuzione in tre impostazioni locali di dispositivi diversi, ma il testo visualizzato in tutti i pulsanti è specifico per le impostazioni locali che ogni dispositivo viene impostato su:

[![Esempi di tre diverse impostazioni locali](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png#lightbox)

In questo esempio, il contenuto di un file di layout **Main. axml** simile al seguente:

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

![Stringhe di risorsa per tre lingue](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Stringhe di risorsa per tre lingue](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Localizzazione delle App Android

Leggere il [Introduzione alla localizzazione](~/cross-platform/app-fundamentals/localization.md) per suggerimenti e linee guida sulla localizzazione delle App per dispositivi mobili.

Il [localizzazione di applicazioni Android](~/android/app-fundamentals/localization.md) Guida contiene esempi più specifici su come convertire le stringhe e localizzare le immagini usando xamarin. Android.



## <a name="related-links"></a>Collegamenti correlati

- [Localizzazione delle App Android](~/android/app-fundamentals/localization.md)
- [Panoramica sulla localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
