---
title: Risorse Android
description: In questo articolo viene introdotto il concetto di risorse Android in Xamarin.Android e viene documentato come utilizzarle. Illustra come usare le risorse nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi, incluse dimensioni e densità dello schermo diverse.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/01/2018
ms.openlocfilehash: 1ff4d9d896aaa5f290402a49aa4b4bd1f1e00aaf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025052"
---
# <a name="android-resources"></a>Risorse Android

_In questo articolo viene introdotto il concetto di risorse Android in Xamarin.Android e viene documentato come utilizzarle. Illustra come usare le risorse nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi, incluse dimensioni e densità dello schermo diverse._

## <a name="overview"></a>Panoramica

Un'applicazione Android è raramente solo codice sorgente. Spesso sono presenti molti altri file che costituiscono un'applicazione: video, immagini, tipi di carattere e file audio, solo per citarne alcuni. Complessivamente, questi file di codice non di origine vengono definiti risorse e vengono compilati (insieme al codice sorgente) durante il processo di compilazione e assemblati come APK per la distribuzione e l'installazione sui dispositivi:

![Diagramma di creazione pacchetti](images/packaging-diagram.png)

Le risorse offrono diversi vantaggi a un'applicazione Android:

- La **separazione del codice** &ndash; separa il codice sorgente da immagini, stringhe, menu, animazioni, colori e così via. Poiché tali risorse possono essere utili in modo significativo durante la localizzazione.

- Usare **più dispositivi come destinazione** &ndash; offre un supporto più semplice per configurazioni del dispositivo diverse senza modifiche al codice.

- Il **controllo in fase di compilazione** &ndash; risorse sono statiche e compilate nell'applicazione. Ciò consente di controllare l'utilizzo delle risorse in fase di compilazione, quando sarà facile intercettare e correggere gli errori, anziché in fase di esecuzione quando risulta più difficile individuare e rendere più costoso la correzione.

Quando viene avviato un nuovo progetto Xamarin.Android, viene creata una directory speciale denominata Resources, insieme ad alcune sottodirectory:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Contenuto e cartella delle risorse](images/resources-folder-vs.png)

Nell'immagine precedente, le risorse dell'applicazione sono organizzate in base al relativo tipo in queste sottodirectory: le immagini vengono inserite nella directory di cui è possibile eseguire l' **estrazione** . le visualizzazioni vengono inserite nella sottodirectory di **layout** e così via.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Contenuto e cartella delle risorse](images/resources-folder-xs.png)

Nell'immagine precedente, le risorse dell'applicazione sono organizzate in base al relativo tipo nelle sottodirectory: le immagini verranno inserite nella directory **mipmap** le visualizzazioni vengono inserite nella sottodirectory di **layout** e così via.

-----

Esistono due modi per accedere a queste risorse in un'applicazione Xamarin.Android: a *livello* di codice nel codice e in *modo dichiarativo* in XML usando una sintassi XML speciale.

Queste risorse sono denominate *risorse predefinite* e vengono usate da tutti i dispositivi a meno che non venga specificata una corrispondenza più specifica. Inoltre, ogni tipo di risorsa può facoltativamente avere *risorse alternative* che possono essere usate da Android per dispositivi specifici. Ad esempio, è possibile fornire le risorse per specificare le impostazioni locali dell'utente, le dimensioni dello schermo o se il dispositivo viene ruotato di 90 gradi da verticale a orizzontale e così via. In ognuno di questi casi, Android caricherà le risorse per l'uso da parte dell'applicazione senza alcuna ulteriore operazione di codifica da parte dello sviluppatore.

Le risorse alternative vengono specificate aggiungendo una stringa breve, denominata *qualificatore*, alla fine della directory contenente un determinato tipo di risorse.

Ad esempio, **le risorse/disegnatore-de** specificheranno le immagini per i dispositivi impostati su impostazioni locali tedesche, mentre **le risorse/disegnabili-fr** terrebbe le immagini per i dispositivi impostati su impostazioni locali francesi. Un esempio di come fornire risorse alternative può essere visualizzato nell'immagine seguente, in cui viene eseguita la stessa applicazione con le impostazioni locali della modifica del dispositivo:

![Schermate di esempio per impostazioni locali diverse](images/localized-screenshots.png)

Questo articolo illustra in maniera completa l'uso delle risorse e illustra gli argomenti seguenti:

- Le **nozioni di base sulle risorse di Android** &ndash; uso delle risorse predefinite a livello di codice e in modo dichiarativo.

- **Configurazioni specifiche del dispositivo** &ndash; supportano le diverse risoluzioni e densità dello schermo in un'applicazione.

- **Localizzazione** &ndash; utilizzo di risorse per supportare le diverse aree in cui è possibile utilizzare un'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Uso di asset Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Nozioni di base sulle applicazioni](https://developer.android.com/guide/topics/fundamentals.html)
- [Risorse di applicazioni](https://developer.android.com/guide/topics/resources/index.html)
- [Supporto di più schermate](https://developer.android.com/guide/practices/screens_support.html)
