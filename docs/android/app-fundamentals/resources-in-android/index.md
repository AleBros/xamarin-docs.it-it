---
title: Risorse Android
description: "In questo articolo viene introdotto il concetto di risorse Android di xamarin e verrà documentate come utilizzarle. Viene illustrato come utilizzare le risorse disponibili nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi, tra cui densità e dimensioni dello schermo diverse."
ms.topic: article
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: 6546870d85f7b77e60dff0cb9e6075f982c9cb8e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="android-resources"></a>Risorse Android

_In questo articolo viene introdotto il concetto di risorse Android di xamarin e verrà documentate come utilizzarle. Viene illustrato come utilizzare le risorse disponibili nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi, tra cui densità e dimensioni dello schermo diverse._


## <a name="overview"></a>Panoramica

Raramente, un'applicazione Android è solo il codice sorgente. Spesso esistono numerosi altri file che costituiscono un'applicazione: video, immagini, i tipi di carattere e file audio solo per citarne alcune. Complessivamente, questi file di codice non di origine sono dette risorse e vengono compilati (insieme il codice sorgente) durante il processo di compilazione e compresso come un file APK per la distribuzione e installazione nei dispositivi:

![Diagramma di creazione di pacchetti](images/packaging-diagram.png)

Risorse offrono diversi vantaggi a un'applicazione Android:

-  **Separazione del codice** &ndash; separa il codice sorgente da immagini, stringhe, i menu, animazioni, colori e così via. In quanto tali risorse possono essere utili notevolmente durante la localizzazione.

-  **Più dispositivi di destinazione** &ndash; fornisce il supporto di più semplice diverse configurazioni di dispositivi senza modifiche al codice.

-  **Verifica in fase di compilazione** &ndash; risorse sono statici e compilati nell'applicazione. In questo modo l'utilizzo delle risorse da controllare in fase di compilazione, quando sarà facile rilevare e correggere gli errori, anziché in fase di esecuzione quando è più difficile da individuare e costosa da correggere.

Quando viene avviato un nuovo progetto di xamarin, viene creata una directory speciale denominata risorse, insieme a alcuni le sottodirectory:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Contenuto e la cartella di risorse](images/resources-folder-vs.png)

Nell'immagine precedente, le risorse dell'applicazione sono organizzate in base al tipo in tali sottodirectory: le immagini verranno inseriti nel **drawable** directory; andare viste di **layout** sottodirectory e così via.
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Contenuto e la cartella di risorse](images/resources-folder-xs.png)

Nell'immagine precedente, le risorse dell'applicazione sono organizzate in base al tipo in tali sottodirectory: le immagini verranno inseriti nel **mipmap** directory; andare viste di **layout** sottodirectory e così via.
 
-----

Esistono due modi per accedere alle risorse in un'applicazione di xamarin: *a livello di programmazione* nel codice e *in modo dichiarativo* in XML utilizzando una sintassi XML speciale.

Queste risorse vengono chiamate *risorse predefinite* e vengono utilizzati da tutti i dispositivi a meno che non è specificata una corrispondenza più specifica. Inoltre, ogni tipo di risorsa può facoltativamente avere *risorse alternativo* che Android può usare per specifici dispositivi di destinazione. Ad esempio, possibile specificare le risorse di destinazione delle impostazioni locali dell'utente, le dimensioni dello schermo, o se il dispositivo viene ruotato di 90 gradi da verticale a orizzontale, e così via. In ognuno di questi casi, Android caricherà le risorse da utilizzare per l'applicazione senza un impegno di codifica aggiuntivo dallo sviluppatore.

Le risorse alternative vengono specificate mediante l'aggiunta di una stringa breve, chiamata un *qualificatore*, alla fine della directory che contiene un tipo specifico di risorse.

Ad esempio, **risorse/drawable-de** specificherà le immagini per i dispositivi che utilizzano le impostazioni locali tedesche, mentre **risorse/drawable-fr** conterrebbe immagini per i dispositivi è impostato su in lingua francese. Un esempio di fornire risorse alternative può essere visualizzato nell'immagine seguente in cui la stessa applicazione viene eseguita con le impostazioni locali della modifica di dispositivo:

![Schermate di esempio per impostazioni locali diverse](images/localized-screenshots.png)

In questo articolo verrà un quadro completo utilizzando le risorse e vengono trattati i seguenti argomenti:

-  **Nozioni di base di Android risorsa** &ndash; utilizzando le risorse predefinite a livello di codice e in modo dichiarativo, aggiungere i tipi di risorse quali immagini e i tipi di carattere a un'applicazione.

-  **Le configurazioni specifiche del dispositivo** &ndash; che supportano diverse risoluzioni dello schermo e densità in un'applicazione.

-  **Localizzazione** &ndash; utilizzo delle risorse per supportare le diverse aree di un'applicazione può essere utilizzata.


## <a name="related-links"></a>Collegamenti correlati

- [Uso di asset Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Nozioni di base sulle applicazioni](http://developer.android.com/guide/topics/fundamentals.html)
- [Risorse di applicazioni](http://developer.android.com/guide/topics/resources/index.html)
- [Supporto di più schermate](http://developer.android.com/guide/practices/screens_support.html)
