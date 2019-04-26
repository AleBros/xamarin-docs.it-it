---
title: Risorse predefinite
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: 20865b71cce16f57b84a1c54986bd84180d3e190
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013211"
---
# <a name="default-resources"></a>Risorse predefinite

Risorse predefinite sono elementi che non sono specifici di qualsiasi fattore di forma o un dispositivo specifico e pertanto sono la scelta predefinita per il sistema operativo Android se no più specifiche sono disponibili le risorse. Si tratta di conseguenza, il tipo più comune di risorsa da creare. Sono organizzati in sottodirectory del **risorse** directory in base al relativo tipo di risorsa:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![File di risorse predefiniti](default-resources-images/01-resource-files-vs.png)

Nell'immagine precedente, il progetto contiene i valori predefiniti per le risorse drawable, layout e i valori (file XML che contengono valori semplici).

Di seguito viene fornito un elenco completo dei tipi di risorse:

-  **Animatore** &ndash; file XML che descrivono le animazioni di proprietà.
   Le animazioni di proprietà sono state introdotte nel livello API 11 (Android 3.0) e fornisce l'animazione di proprietà in un oggetto. Le animazioni di proprietà sono un modo più flessibile e potente per descrivere le animazioni in qualsiasi tipo di oggetto.

-  **anim** &ndash; file XML che descrivono *interpolazione* animazioni. Le animazioni di interpolazione sono una serie di istruzioni di animazione per eseguire trasformazioni sul contenuto di una rotazione di oggetto o un esempio, visualizza un'immagine o aumentano le dimensioni del testo. Le animazioni di interpolazione sono limitate per visualizzare solo gli oggetti.

-  **colore** &ndash; file XML che descrivono un elenco di stato di colori. Per comprendere gli elenchi di stato di colore, prendere in considerazione un widget dell'interfaccia utente, ad esempio un pulsante.
   Potrebbe avere diversi stati, ad esempio premuto o disabilitato e il pulsante può cambiare colore a ogni modifica nello stato. L'elenco viene espresso in un elenco di stati.

-  **drawable** &ndash; risorse Drawable sono un concetto generale per la grafica che può essere compilato nell'applicazione e quindi si accede da chiamate API o a cui fanno riferimento ad altre risorse XML.
   Alcuni esempi di drawable sono i file bitmap (con estensione png, GIF, jpg), nota come le immagini bitmap ridimensionabili speciali [nove patch](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), dello stato sono elencate, forme generiche definite in XML e così via.
 
-  **layout** &ndash; file XML che descrivono un layout di interfaccia utente, ad esempio un'attività o una riga in un elenco.

-  **menu di scelta** &ndash; file XML che descrivono menu applicazione, ad esempio *opzioni di menu*, *menu di scelta rapida*, e *sottomenu*. Per un esempio di menu, vedere la [Demo di Menu Popup](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) o nella [controlli Standard](https://developer.xamarin.com/samples/mobile/StandardControls/) esempio.

-  **non elaborato** &ndash; file arbitrari che vengono salvati nella relativa forma binaria non elaborata. Questi file vengono compilati in un'applicazione Android in un formato binario.

-  **i valori** &ndash; file XML che contengono valori semplici. Un file XML nella directory di valori non definisce una sola risorsa, ma è invece possibile definire più risorse. Ad esempio un file XML può contenere un elenco di valori stringa, mentre un altro file XML può contenere un elenco di valori colore.

-  **XML** &ndash; file XML che hanno una funzione simili ai file di configurazione .NET. Si tratta di contenuto XML arbitrario che può essere letta in fase di esecuzione dall'applicazione.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![File di risorse predefiniti](default-resources-images/01-resource-files-xs.png)

Nell'immagine precedente, il progetto contiene i valori predefiniti per le risorse drawable, layout e i valori (file XML che contengono valori semplici).

Di seguito viene fornito un elenco completo dei tipi di risorse:

-  **Animatore** &ndash; file XML che descrivono le animazioni di proprietà.
   Le animazioni di proprietà sono state introdotte nel livello API 11 (Android 3.0) e fornisce l'animazione di proprietà in un oggetto. Le animazioni di proprietà sono un modo più flessibile e potente per descrivere le animazioni in qualsiasi tipo di oggetto.

-  **anim** &ndash; file XML che descrivono *interpolazione* animazioni. Le animazioni di interpolazione sono una serie di istruzioni di animazione per eseguire trasformazioni sul contenuto di una rotazione di oggetto o un esempio, visualizza un'immagine o aumentano le dimensioni del testo. Le animazioni di interpolazione sono limitate per visualizzare solo gli oggetti.

-  **colore** &ndash; file XML che descrivono un elenco di stato di colori. Per comprendere gli elenchi di stato di colore, prendere in considerazione un widget dell'interfaccia utente, ad esempio un pulsante.
   Può essere presentare diversi stati come premuto o disabilitato e il pulsante può cambiare colore a ogni modifica nello stato. L'elenco viene espresso in un elenco di stati.

-  **tipo di carattere** &ndash; a partire da 26 a livello di API, è possibile incorporare i tipi di carattere come una risorsa in un'applicazione Android. 26 la libreria di supporto verrà esegue il backporting i tipi di carattere per il livello API 14. Incorporamento di tipi di carattere consente alle applicazioni di caricare i tipi di carattere personalizzati direttamente dal layout XML senza la necessità di importarli come asset prima dell'uso.

-  **mipmap** &ndash; risorse Drawable sono un concetto generale per la grafica che può essere compilato nell'applicazione e quindi si accede da chiamate API o a cui fanno riferimento ad altre risorse XML.
   Alcuni esempi di drawable sono i file bitmap (con estensione png, GIF, jpg), nota come le immagini bitmap ridimensionabili speciali [nove patch](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), dello stato sono elencate, forme generiche definite in XML e così via.

-  **layout** &ndash; file XML che descrivono un layout di interfaccia utente, ad esempio un'attività o una riga in un elenco.

-  **menu di scelta** &ndash; file XML che descrivono menu applicazione, ad esempio *opzioni di menu*, *menu di scelta rapida*, e *sottomenu*. Per un esempio di menu, vedere la [Demo di Menu Popup](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) o nella [controlli Standard](https://developer.xamarin.com/samples/mobile/StandardControls/) esempio.

-  **non elaborato** &ndash; file arbitrari che vengono salvati nella relativa forma binaria non elaborata. Questi file vengono compilati in un'applicazione Android in un formato binario.

-  **i valori** &ndash; file XML che contengono valori semplici. Un file XML nella directory di valori non definisce una sola risorsa, ma è invece possibile definire più risorse. Ad esempio un file XML può contenere un elenco di valori stringa, mentre un altro file XML può contenere un elenco di valori colore.

-  **XML** &ndash; file XML che hanno una funzione simili ai file di configurazione .NET. Si tratta di contenuto XML arbitrario che può essere letta in fase di esecuzione dall'applicazione

-----
