---
title: Risorse predefinite
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/13/2018
ms.openlocfilehash: 119aa8b967ace858ee56f521624f6356e08a8b80
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="default-resources"></a>Risorse predefinite

Risorse predefinite sono elementi che non sono specifiche di qualsiasi fattore di forma o un dispositivo specifico e pertanto la scelta predefinita dal sistema operativo Android se non più specifica le risorse disponibili. Di conseguenza, si tratta il tipo più comune di risorsa da creare. Sono organizzati in sottodirectory del **risorse** directory in base al tipo di risorsa:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![File di risorse predefiniti](default-resources-images/01-resource-files-vs.png)

Nell'immagine precedente, il progetto contiene i valori predefiniti per le risorse drawable, layout e i valori (file XML che contengono valori semplici).

Un elenco completo dei tipi di risorse riportato di seguito:

-  **Animatore** &ndash; file XML che descrivono le animazioni di proprietà.
   Le animazioni di proprietà sono state introdotte nel livello API 11 (Android 3.0) e fornisce per l'animazione della proprietà di un oggetto. Le animazioni di proprietà sono un modo più flessibile e potente per descrivere le animazioni in qualsiasi tipo di oggetto.

-  **anim** &ndash; file XML che descrivono *interpolazione* animazioni. Interpolazioni sono una serie di istruzioni di animazione per l'esecuzione di trasformazioni al contenuto di una rotazione di oggetto, o un esempio, visualizza un'immagine o l'aumento delle dimensioni del testo. Interpolazioni sono limitati a visualizzare solo gli oggetti.

-  **colore** &ndash; file XML che descrivono un elenco di stato di colori. Per comprendere gli elenchi di stato di colore, considerare un widget dell'interfaccia utente, ad esempio un pulsante.
   Possono avere stati diversi, ad esempio premuto o disattivato e il pulsante potrebbe cambiare colore a ogni modifica di stato. L'elenco è espresso in un elenco di stato.

-  **drawable** &ndash; Drawable risorse sono un concetto generale per gli elementi grafici che possono essere compilati nell'applicazione e quindi accedere tramite chiamate API o a cui fa riferimento ad altre risorse XML.
   Alcuni esempi di drawables sono file bitmap (con estensione png, GIF, jpg), le bitmap di ridimensionabile speciale note come [nove patch](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), elenca lo stato, forme generiche definite in XML e così via.
 
-  **layout** &ndash; file XML che descrivono un layout dell'interfaccia utente, ad esempio un'attività o di una riga in un elenco.

-  **menu** &ndash; file XML che descrivono i menu dell'applicazione, ad esempio *opzioni menu*, *menu di scelta rapida*, e *sottomenu*. Per un esempio di menu, vedere il [Demo Menu Popup](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) o [controlli Standard](https://developer.xamarin.com/samples/mobile/StandardControls/) esempio.

-  **Raw** &ndash; file arbitrari che vengono salvati nella relativa forma binaria non elaborata. Questi file vengono compilati in un'applicazione Android in un formato binario.

-  **i valori** &ndash; file XML che contengono valori semplici. Un file XML nella directory di valori non definisce una singola risorsa, ma è invece possibile definire più risorse. Ad esempio un file XML può contenere un elenco di valori stringa, mentre un altro file XML può contenere un elenco di valori di colore.

-  **XML** &ndash; file XML che sono simili in funzione per i file di configurazione .NET. Si tratta di contenuto XML arbitrario che può essere letto in fase di esecuzione dall'applicazione


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![File di risorse predefiniti](default-resources-images/01-resource-files-xs.png)

Nell'immagine precedente, il progetto contiene i valori predefiniti per le risorse drawable, layout e i valori (file XML che contengono valori semplici).

Un elenco completo dei tipi di risorse riportato di seguito:

-  **Animatore** &ndash; file XML che descrivono le animazioni di proprietà.
   Le animazioni di proprietà sono state introdotte nel livello API 11 (Android 3.0) e fornisce per l'animazione della proprietà di un oggetto. Le animazioni di proprietà sono un modo più flessibile e potente per descrivere le animazioni in qualsiasi tipo di oggetto.

-  **anim** &ndash; file XML che descrivono *interpolazione* animazioni. Interpolazioni sono una serie di istruzioni di animazione per l'esecuzione di trasformazioni al contenuto di una rotazione di oggetto, o un esempio, visualizza un'immagine o l'aumento delle dimensioni del testo. Interpolazioni sono limitati a visualizzare solo gli oggetti.

-  **colore** &ndash; file XML che descrivono un elenco di stato di colori. Per comprendere gli elenchi di stato di colore, considerare un widget dell'interfaccia utente, ad esempio un pulsante.
   Può avere diversi stati, ad esempio premuto o disattivato e il pulsante potrebbe cambiare colore a ogni modifica di stato. L'elenco è espresso in un elenco di stato.

-  **tipo di carattere** &ndash; a partire da 26 livello dell'API, è possibile incorporare i tipi di carattere come risorsa in un'applicazione Android. Il 26 libreria di supporto verranno backport i tipi di carattere per il livello di API 14. Incorporamento di tipi di carattere consente alle applicazioni di

-  **mipmap** &ndash; Drawable risorse sono un concetto generale per gli elementi grafici che possono essere compilati nell'applicazione e quindi accedere tramite chiamate API o a cui fa riferimento ad altre risorse XML.
   Alcuni esempi di drawables sono file bitmap (con estensione png, GIF, jpg), le bitmap di ridimensionabile speciale note come [nove patch](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), elenca lo stato, forme generiche definite in XML e così via.

-  **layout** &ndash; file XML che descrivono un layout dell'interfaccia utente, ad esempio un'attività o di una riga in un elenco.

-  **menu** &ndash; file XML che descrivono i menu dell'applicazione, ad esempio *opzioni menu*, *menu di scelta rapida*, e *sottomenu*. Per un esempio di menu, vedere il [Demo Menu Popup](https://developer.xamarin.com/samples/monodroid/PopupMenuDemo/) o [controlli Standard](https://developer.xamarin.com/samples/mobile/StandardControls/) esempio.

-  **Raw** &ndash; file arbitrari che vengono salvati nella relativa forma binaria non elaborata. Questi file vengono compilati in un'applicazione Android in un formato binario.

-  **i valori** &ndash; file XML che contengono valori semplici. Un file XML nella directory di valori non definisce una singola risorsa, ma è invece possibile definire più risorse. Ad esempio un file XML può contenere un elenco di valori stringa, mentre un altro file XML può contenere un elenco di valori di colore.

-  **XML** &ndash; file XML che sono simili in funzione per i file di configurazione .NET. Si tratta di contenuto XML arbitrario che può essere letto in fase di esecuzione dall'applicazione

-----
