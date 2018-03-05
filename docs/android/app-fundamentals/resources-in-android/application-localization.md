---
title: Localizzazione di applicazioni e risorse di tipo stringa
ms.topic: article
ms.prod: xamarin
ms.assetid: 374A9DA6-1853-8B98-6954-7FE3F591C07C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/30/2017
ms.openlocfilehash: 9c65672ef2c3f968e76c6180da07f5daf9f5b68a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="application-localization-and-string-resources"></a>Localizzazione di applicazioni e risorse di tipo stringa

La localizzazione dell'applicazione consiste nel fornire risorse alternative per una regione o specifica delle impostazioni locali di destinazione. Ad esempio, è possibile fornire stringhe localizzate per diversi paesi o è possibile modificare i colori o layout per la corrispondenza delle impostazioni cultura specifiche. Android caricherà e utilizzare le risorse appropriate per le impostazioni locali del dispositivo in fase di esecuzione senza apportare modifiche al codice sorgente.

Ad esempio, l'immagine seguente mostra la stessa applicazione in esecuzione in tre impostazioni locali di dispositivi diversi, ma il testo visualizzato in ogni pulsante è specifico per le impostazioni locali che ogni dispositivo è impostato su:

[![Esempi di tre diverse impostazioni locali](application-localization-images/01-click-me-sml.png)](application-localization-images/01-click-me.png)

In questo esempio, il contenuto di un file di layout, **axml** simile al seguente:

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

Nell'esempio precedente, la stringa per il pulsante è stata caricata dalle risorse fornendo l'ID di risorsa per la stringa:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Stringhe di risorsa per tre lingue](application-localization-images/02-resource-strings-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Stringhe di risorsa per tre lingue](application-localization-images/02-resource-strings-xs.png)
 
-----
 
## <a name="localizing-android-apps"></a>Localizzazione di applicazioni Android

Lettura di [Introduzione alla localizzazione](~/cross-platform/app-fundamentals/localization.md) per suggerimenti e informazioni sulla localizzazione di applicazioni per dispositivi mobili.

Il [localizzazione di applicazioni Android](~/android/app-fundamentals/localization.md) Guida contiene esempi più specifici su come convertire le stringhe e localizzare immagini usando xamarin.



## <a name="related-links"></a>Collegamenti correlati

- [Localizzazione di applicazioni Android](~/android/app-fundamentals/localization.md)
- [Cenni preliminari sulla localizzazione multipiattaforma](~/cross-platform/app-fundamentals/localization.md)
