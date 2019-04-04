---
title: Risorse Android
description: Questo articolo introduce il concetto di risorse Android in xamarin. Android e verrà documentati come usarli. Viene illustrato come usare le risorse nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi tra cui densità e dimensioni dello schermo diverse.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: f14b3fd31fdda200f51f429367465677d389b1ca
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666368"
---
# <a name="android-resources"></a>Risorse Android

_Questo articolo introduce il concetto di risorse Android in xamarin. Android e verrà documentati come usarli. Viene illustrato come usare le risorse nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi tra cui densità e dimensioni dello schermo diverse._


## <a name="overview"></a>Panoramica

Un'applicazione Android raramente è semplicemente il codice sorgente. Spesso esistono molti altri file che costituiscono un'applicazione: video, immagini, tipi di carattere e i file audio solo per citarne alcuni. Complessivamente, questi file di codice non di origine vengono definiti le risorse e vengono compilati (con il codice sorgente) durante il processo di compilazione e compresso come un file APK per la distribuzione e l'installazione nei dispositivi:

![Diagramma di creazione di pacchetti](images/packaging-diagram.png)

Risorse offrono diversi vantaggi per un'applicazione Android:

-  **Separazione del codice** &ndash; separa il codice sorgente da immagini, stringhe, i menu, animazioni, colori e così via. In quanto tali risorse possono risultare utili notevolmente durante la localizzazione.

-  **Più dispositivi di destinazione** &ndash; offerto un supporto più semplice delle diverse configurazioni dei dispositivi senza modifiche al codice.

-  **Verifica della fase di compilazione** &ndash; risorse sono statici e compilato nell'applicazione. In questo modo l'utilizzo delle risorse deve essere controllata in fase di compilazione, quando sarà più facile rilevare e correggere gli errori, anziché in fase di esecuzione quando è più difficile da individuare e correggere i costi.

Quando viene avviato un nuovo progetto xamarin. Android, una directory speciale denominata risorse viene creata, insieme a alcune sottodirectory:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Contenuto e la cartella di risorse](images/resources-folder-vs.png)

Nell'immagine precedente, le risorse dell'applicazione sono organizzate in base al relativo tipo in tali sottodirectory: immagini entra **drawable** directory; viste andare **layout** sottodirectory e così via.
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Contenuto e la cartella di risorse](images/resources-folder-xs.png)

Nell'immagine precedente, le risorse dell'applicazione sono organizzate in base al relativo tipo in tali sottodirectory: immagini entra **mipmap** directory; viste andare **layout** sottodirectory e così via.
 
-----

Esistono due modi per accedere alle risorse in un'applicazione xamarin. Android: *a livello di programmazione* nel codice e *in modo dichiarativo* nel codice XML usando una speciale sintassi XML.

Queste risorse vengono chiamate *risorse predefinite* e vengono utilizzati da tutti i dispositivi a meno che non è specificata una corrispondenza più specifica. Inoltre, ogni tipo di risorsa facoltativamente presentare *risorse alternative* che Android può usare come destinazione dispositivi specifici. Ad esempio, le risorse possono essere fornite come destinazione delle impostazioni locali dell'utente, le dimensioni dello schermo, o se il dispositivo viene ruotato di 90 gradi da verticale a orizzontale, e così via. In ognuno di questi casi, Android caricherà le risorse per l'uso per l'applicazione senza alcuno sforzo aggiuntivo codifica dallo sviluppatore.

Le risorse alternative vengono specificate mediante l'aggiunta di una stringa breve, chiamata un' *qualificatore*, alla fine della directory che contiene un tipo specifico di risorse.

Ad esempio, **risorse/drawable-de** specificherà le immagini per i dispositivi che sono impostati su impostazioni locali tedesche, mentre **risorse/drawable-fr** conserverebbero immagini per i dispositivi è impostato su delle impostazioni locali francesi. Un esempio di fornire risorse alternative può essere visualizzato nell'immagine seguente in cui è in esecuzione la stessa applicazione con le impostazioni locali della modifica dispositivo:

![Schermate di esempio per impostazioni locali diverse](images/localized-screenshots.png)

Questo articolo verrà accettano un quadro completo di utilizzo delle risorse e trattano gli argomenti seguenti:

-  **Concetti fondamentali sulle risorse Android** &ndash; usando risorse predefinite a livello di codice e in modo dichiarativo, aggiunta di tipi di risorse quali immagini e i tipi di carattere a un'applicazione.

-  **Le configurazioni specifiche dei dispositivi** &ndash; che supportano le diverse risoluzioni dello schermo e densità in un'applicazione.

-  **Localizzazione** &ndash; utilizzo delle risorse per supportare le diverse aree è possibile usare un'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Uso di asset Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Nozioni di base sulle applicazioni](https://developer.android.com/guide/topics/fundamentals.html)
- [Risorse di applicazioni](https://developer.android.com/guide/topics/resources/index.html)
- [Supporto di più schermate](https://developer.android.com/guide/practices/screens_support.html)
