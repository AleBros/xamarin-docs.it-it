---
title: Risorse predefinite
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: a1f2016af3bcac338f47b7315a26fe50ae76fee7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755093"
---
# <a name="default-resources"></a>Risorse predefinite

Le risorse predefinite sono elementi che non sono specifici di un determinato dispositivo o fattore di forma e pertanto sono la scelta predefinita da parte del sistema operativo Android se non è possibile trovare risorse più specifiche. Di conseguenza, si tratta del tipo più comune di risorsa da creare. Sono organizzati in sottodirectory della directory delle **risorse** in base al relativo tipo di risorsa:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![File di risorse predefiniti](default-resources-images/01-resource-files-vs.png)

Nell'immagine precedente, il progetto ha valori predefiniti per le risorse, i layout e i valori che è stato disegnato, ovvero i file XML che contengono valori semplici.

Di seguito è riportato un elenco completo dei tipi di risorse:

- **animatore** &ndash; File XML che descrivono animazioni di proprietà.
   Le animazioni delle proprietà sono state introdotte in API level 11 (Android 3,0) e forniscono l'animazione delle proprietà in un oggetto. Le animazioni delle proprietà sono un modo più flessibile e potente per descrivere le animazioni in qualsiasi tipo di oggetto.

- **animo** File XML che descrivono animazioni di *interpolazione.* &ndash; Le animazioni di interpolazione sono una serie di istruzioni di animazione che consentono di eseguire trasformazioni sul contenuto di un oggetto visualizzazione o, ad esempio, di ruotare un'immagine o di aumentare le dimensioni del testo. Le animazioni con interpolazione sono limitate solo agli oggetti visualizzazione.

- **colore** &ndash; File XML che descrivono un elenco degli Stati dei colori. Per comprendere gli elenchi di Stati del colore, prendere in considerazione un widget dell'interfaccia utente, ad esempio un pulsante.
   Potrebbe avere stati diversi, ad esempio Pressed o disabled, e il pulsante potrebbe modificare il colore con ogni modifica dello stato. L'elenco è espresso in un elenco di Stati.

- **disegnatore** &ndash; Le risorse di riferimento sono un concetto generale per le immagini che possono essere compilate nell'applicazione e quindi accessibili dalle chiamate API o da altre risorse XML.
   Alcuni esempi di drawables sono file bitmap (PNG, gif, jpg), bitmap ridimensionabili speciali note come [nove patch](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), elenchi di stato, forme generiche definite in XML e così via.

- **layout** di &ndash; File XML che descrivono un layout dell'interfaccia utente, ad esempio un'attività o una riga in un elenco.

- **menu** di File XML che descrivono i menu dell'applicazione, ad esempio *menu opzioni*, *menu di scelta rapida*e *sottomenu.* &ndash; Per un esempio di menu, vedere la [demo del menu popup](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo) o l'esempio di [controlli standard](https://docs.microsoft.com/samples/xamarin/mobile-samples/standardcontrols/) .

- non **elaborati** &ndash; File arbitrari salvati nel formato binario non elaborato. Questi file vengono compilati in un'applicazione Android in formato binario.

- **valori** di &ndash; File XML che contengono valori semplici. Un file XML nella directory values non definisce una singola risorsa, ma può invece definire più risorse. Un file XML, ad esempio, può avere un elenco di valori di stringa, mentre un altro file XML può avere un elenco di valori dei colori.

- file XML XML che funzionano in modo simile ai file di configurazione .NET. &ndash; Si tratta di codice XML arbitrario che può essere letto in fase di esecuzione dall'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![File di risorse predefiniti](default-resources-images/01-resource-files-xs.png)

Nell'immagine precedente, il progetto ha valori predefiniti per le risorse, i layout e i valori che è stato disegnato, ovvero i file XML che contengono valori semplici.

Di seguito è riportato un elenco completo dei tipi di risorse:

- **animatore** &ndash; File XML che descrivono animazioni di proprietà.
   Le animazioni delle proprietà sono state introdotte in API level 11 (Android 3,0) e forniscono l'animazione delle proprietà in un oggetto. Le animazioni delle proprietà sono un modo più flessibile e potente per descrivere le animazioni in qualsiasi tipo di oggetto.

- **animo** File XML che descrivono animazioni di *interpolazione.* &ndash; Le animazioni di interpolazione sono una serie di istruzioni di animazione che consentono di eseguire trasformazioni sul contenuto di un oggetto visualizzazione o, ad esempio, di ruotare un'immagine o di aumentare le dimensioni del testo. Le animazioni con interpolazione sono limitate solo agli oggetti visualizzazione.

- **colore** &ndash; File XML che descrivono un elenco degli Stati dei colori. Per comprendere gli elenchi di Stati del colore, prendere in considerazione un widget dell'interfaccia utente, ad esempio un pulsante.
   È possibile che si verifichino stati diversi, ad esempio Pressed o disabled, e che il pulsante modifichi il colore con ogni modifica dello stato. L'elenco è espresso in un elenco di Stati.

- **carattere** &ndash; A partire dal livello API 26, è possibile incorporare i tipi di carattere come risorsa in un'applicazione Android. La libreria di supporto 26 backporting i tipi di carattere a livello API 14. L'incorporamento dei tipi di carattere consente alle applicazioni di caricare tipi di carattere personalizzati direttamente dai layout XML senza che sia necessario importarli come asset prima di usarli.

- **mipmap** &ndash; Le risorse di riferimento sono un concetto generale per le immagini che possono essere compilate nell'applicazione e quindi accessibili dalle chiamate API o da altre risorse XML.
   Alcuni esempi di drawables sono file bitmap (PNG, gif, jpg), bitmap ridimensionabili speciali note come [nove patch](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), elenchi di stato, forme generiche definite in XML e così via.

- **layout** di &ndash; File XML che descrivono un layout dell'interfaccia utente, ad esempio un'attività o una riga in un elenco.

- **menu** di File XML che descrivono i menu dell'applicazione, ad esempio *menu opzioni*, *menu di scelta rapida*e *sottomenu.* &ndash; Per un esempio di menu, vedere la [demo del menu popup](https://docs.microsoft.com/samples/xamarin/monodroid-samples/popupmenudemo) o l'esempio di [controlli standard](https://docs.microsoft.com/samples/xamarin/mobile-samples/standardcontrols/) .

- non **elaborati** &ndash; File arbitrari salvati nel formato binario non elaborato. Questi file vengono compilati in un'applicazione Android in formato binario.

- **valori** di &ndash; File XML che contengono valori semplici. Un file XML nella directory values non definisce una singola risorsa, ma può invece definire più risorse. Un file XML, ad esempio, può avere un elenco di valori di stringa, mentre un altro file XML può avere un elenco di valori dei colori.

- file XML XML che funzionano in modo simile ai file di configurazione .NET. &ndash; Si tratta di codice XML arbitrario che può essere letto in fase di esecuzione dall'applicazione

-----
