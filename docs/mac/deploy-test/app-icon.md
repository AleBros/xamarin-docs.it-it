---
title: Icona dell'applicazione per le app Xamarin.Mac
description: Questo articolo illustra la creazione delle immagini necessarie per l'icona di un'applicazione Xamarin.Mac, l'aggregazione delle immagini in un file con estensione icns e l'inclusione dell'icona nel progetto Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 675b9405-d9a7-49f0-94ad-417f10a71d11
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5245ce6c2c8a2317e18a90514039d36232e933ef
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117909"
---
# <a name="application-icon-for-xamarinmac-apps"></a>Icona dell'applicazione per le app Xamarin.Mac

_Questo articolo illustra la creazione delle immagini necessarie per l'icona di un'applicazione Xamarin.Mac, l'aggregazione delle immagini in un file con estensione icns e l'inclusione dell'icona nel progetto Xamarin.Mac._


## <a name="overview"></a>Panoramica

Uno sviluppatore che lavora in C# e .NET in un'applicazione Xamarin.Mac ha accesso agli stessi strumenti per immagini e icone di uno sviluppatore che lavora in *Objective-C* e *Xcode*.

Un'icona efficace dovrebbe comunicare lo scopo principale dell'app Xamarin.Mac e suggerire l'esperienza che l'utente dovrebbe aspettarsi dall'uso dell'app. Questo articolo descrive tutti i passaggi da eseguire per creare gli asset delle immagini necessari per un'icona, per creare un pacchetto di tali asset in un file `AppIcon.appiconset` e per utilizzare il file in un'app Xamarin.Mac.

![Editor AppIcon.appiconset](app-icon-images/intro01.png "Editor AppIcon.appiconset")


## <a name="application-icon"></a>Icona dell'applicazione

Un'icona efficace dovrebbe comunicare lo scopo principale di un'app Xamarin.Mac e suggerire l'esperienza che l'utente dovrebbe aspettarsi dall'uso di un'app. Ogni app macOS deve includere diverse dimensioni della propria icona da visualizzare nel Finder, nel Dock, nel Launchpad e in altre posizioni nel computer.


## <a name="designing-the-icon"></a>Progettazione dell'icona

Quando si vuole progettare l'icona di un'applicazione, seguire questi suggerimenti di Apple:

- Assegnare all'icona una forma realistica ed esclusiva.
- Se l'app macOS ha una controparte iOS, non riutilizzare l'icona dell'app iOS.
- Usare un'immagine universale che possa essere riconosciuta facilmente dagli utenti.
- Privilegiare la semplicità.
- Usare colori e ombreggiature con moderazione per comunicare la funzione dell'app attraverso l'icona.
- Evitare di mischiare testo reale con testo _illeggibile_ o con linee che simulano testo.
- Creare una versione idealizzata dell'oggetto dell'icona invece di usare una foto reale.
- Evitare di usare elementi dell'interfaccia utente macOS nelle icone.
- Non usare repliche di icone Apple nelle icone.

Leggere le sezioni [App Icon Gallery](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Gallery.html#//apple_ref/doc/uid/20000957-CH88-SW1) (Raccolta di icone delle app) e [Designing App Icons](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Designing.html#//apple_ref/doc/uid/20000957-CH87-SW1) (Progettazione di icone delle app) di [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Linee guida dell'interfaccia umana OS X) di Apple prima di progettare l'icona di un'app Xamarin.Mac.


## <a name="required-image-sizes-and-filenames"></a>Dimensioni delle immagini necessarie e nomi file

Come qualsiasi altra risorsa immagine usata dallo sviluppatore in un'app Xamarin.Mac, l'icona dell'app deve avere sia una versione standard sia una versione con risoluzione Retina. Inoltre, come qualsiasi altra immagine, usare un formato `@2x` per la denominazione dei file di icona:

- **Risoluzione standard**  - _NomeImmagine_**.**_nomefile-estensione_ (Esempio: **icon_512x512.png**)
- **Alta risoluzione**  - _NomeImmagine_**@2x** _nomefile-estensione_ (Esempio: **icon_512x512@2x.png**)

Ad esempio, per fornire la versione 512 x 512 dell'icona dell'app, il file deve essere denominato **icon_512x512.png** e **icon_512x512@2x.png**.

Per assicurarsi che l'icona abbia un aspetto ottimale in tutte le posizioni in cui gli utenti la vedono, fornire risorse nelle dimensioni elencate di seguito:

|Nomefile|Dimensioni in pixel|
|---|---|
|icon_512x512@2x.png|1024 x 1024|
|icon_512x512.png|512 x 512|
|icon_256x256@2x.png|512 x 512|
|icon_256x256.png|256 x 256|
|icon_128x128@2x.png|256 x 256|
|icon_128x128.png|128 x 128|
|icon_32x32@2x.png|64 x 64|
|icon_32x32.png|32 x 32|
|icon_16x16@2x.png|32 x 32|
|icon_16x16.png|16 x 16|

Per altre informazioni, vedere la documentazione di Apple [Provide High-Resolution Versions of All App Graphics Resources](https://developer.apple.com/library/mac/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Optimizing/Optimizing.html#//apple_ref/doc/uid/TP40012302-CH7-SW3) (Fornire versioni ad alta risoluzione per tutte le risorse grafiche delle app).


## <a name="packaging-the-icon-resources"></a>Creazione di pacchetti di risorse delle icone

Se l'icona è progettata e salvata con i nomi e le dimensioni del file corretti, Visual Studio per Mac può assegnarli facilmente agli asset di immagini da usare in Xamarin.Mac.

Seguire questa procedura:

1. Nel **riquadro della soluzione** aprire **Assets.xcassets** > **AppIcons.appiconset**: 

    ![Modifica di AppIcon.appiconset](app-icon-images/intro01.png "Modifica di AppIcon.appiconset")
2. Per ogni dimensione di icona necessaria fare clic sull'icona e selezionare il file di immagine corrispondente precedentemente creato: 

    [![Selezione di un'immagine di un'icona](app-icon-images/intro02.png "Selezione di un'immagine di un'icona")](app-icon-images/intro02-large.png#lightbox)
3. Salvare le modifiche.


## <a name="using-the-icon"></a>Uso dell'icona

Dopo che il file `AppIcon.appiconset` è stato compilato, è necessario assegnarlo al progetto Xamarin.Mac in Visual Studio per Mac.

Seguire questa procedura:

1. Fare doppio clic su **Info.plist** nel **riquadro della soluzione** per aprire **Opzioni progetto**.
2. Nella sezione **Mac OS X Application Target** (Destinazione applicazione Mac OS X) fare clic su **Icone app** per selezionare il file `AppIcon.appiconset`: 

    [![Impostazione del set di icone](app-icon-images/icon01.png "Impostazione del set di icone")](app-icon-images/icon01-large.png#lightbox)
3. Salvare le modifiche.

Quando l'app viene eseguita, la nuova icona viene visualizzata nel dock:

![Esempio di icona di app nel dock macOS](app-icon-images/icon04.png "Esempio di icona di app nel dock macOS")


## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato in modo dettagliato la creazione delle immagini necessarie per l'icona di un'app macOS, la creazione di un pacchetto di risorse dell'icona e l'inclusione dell'icona in un progetto Xamarin.Mac.


## <a name="related-links"></a>Collegamenti correlati

- [MacImages (sample)](https://developer.xamarin.com/samples/mac/MacImages/) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Uso delle immagini](~/mac/app-fundamentals/image.md)
- [macOS Human Interface Guidelines - Icons and Images](https://developer.apple.com/macos/human-interface-guidelines/icons-and-images/image-size-and-resolution/) (Linee guida dell'interfaccia umana macOS - Icone e immagini)
- [About High Resolution for OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) (Informazioni sulla risoluzione elevata per OS X)
- [Icns Builder](https://itunes.apple.com/us/app/icns-builder/id554660130?mt=12) (Generatore di file con estensione icns)
