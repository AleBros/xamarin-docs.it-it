---
title: Implementazione di un lettore video
description: Questo articolo illustra come implementare un'applicazione lettore video con Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 00697ca0adf3a34abec90c2f96d9fd9c273d06bb
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35239784"
---
# <a name="implementing-a-video-player"></a>Implementazione di un lettore video

Talvolta è utile riprodurre file video in un'applicazione Xamarin.Forms. Questa serie di articoli illustra come scrivere un renderer personalizzato per iOS, Android e la piattaforma UWP (Universal Windows Platform) per una classe di Xamarin.Forms denominata `VideoPlayer`.

Nell'esempio [**VideoPlayerDemos**](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) tutti i file che implementano e supportano `VideoPlayer` si trovano in cartelle denominate `FormsVideoLibrary` e identificate con lo spazio dei nomi `FormsVideoLibrary` o con spazi dei nomi che iniziano con `FormsVideoLibrary`. Questa organizzazione e denominazione semplifica la copia dei file del lettore video nella propria soluzione Xamarin.Forms.

`VideoPlayer` può riprodurre file video da tre tipi di origini:

- Internet, tramite un URL
- Una risorsa incorporata nell'applicazione della piattaforma
- La raccolta di video del dispositivo

I lettori video richiedono *controlli del trasporto*, che sono pulsanti per la riproduzione e la messa in pausa del video, e una barra di posizionamento che indica l'avanzamento nel video e consente all'utente di passare rapidamente a una posizione diversa. `VideoPlayer` può usare i controlli del trasporto e la barra di posizionamento forniti dalla piattaforma (come illustrato di seguito) oppure è possibile fornire una barra di posizionamento e controlli del trasporto personalizzati. Ecco il programma in esecuzione in iOS, Android e nella piattaforma UWP (Universal Windows Platform):

[![Riproduci video Web](web-videos-images/playwebvideo-small.png "Riproduci video Web")](web-videos-images/playwebvideo-large.png#lightbox "Riproduci video Web")

È possibile collocare il telefono in posizione orizzontale per espandere la visualizzazione.

Un lettore video più sofisticato può avere funzionalità aggiuntive, ad esempio il controllo volume, un meccanismo che interrompe il video quando arriva una chiamata telefonica o un metodo per mantenere la schermata attiva durante la riproduzione del video.

La serie di articoli seguente illustra in modo progressivo le modalità di compilazione dei renderer di piattaforma e delle classi di supporto:

## <a name="creating-the-platform-video-playersplayer-creationmd"></a>[Creazione di lettori video per le piattaforme](player-creation.md)

Ogni piattaforma richiede una classe `VideoPlayerRenderer` che crea e gestisce un controllo lettore video supportato dalla piattaforma. Questo articolo illustra la struttura delle classi renderer e le modalità per la creazione dei lettori.

## <a name="playing-a-web-videoweb-videosmd"></a>[Riproduzione di un video Web](web-videos.md)

L'origine più comune di video per un lettore video è probabilmente Internet. Questo articolo descrive come creare un riferimento a un video Web e usarlo come origine per il lettore video.

## <a name="binding-video-sources-to-the-playersource-bindingsmd"></a>[Associazione di origini video al lettore](source-bindings.md)

Questo articolo usa un elemento `ListView` per presentare una raccolta di video da riprodurre. Un programma illustra come il file code-behind può impostare l'origine video del lettore video, mentre un altro programma indica come usare il data binding tra `ListView` e il lettore video.

## <a name="loading-application-resource-videosloading-resourcesmd"></a>[Caricamento delle risorse dell'applicazione video](loading-resources.md)

I video possono essere incorporati come risorse nei progetti della piattaforma. Questo articolo illustra come archiviare le risorse e come caricarle in un secondo momento nel programma per la riproduzione nel lettore video.

## <a name="accessing-the-devices-video-libraryaccessing-librarymd"></a>[Accesso alla raccolta di video del dispositivo](accessing-library.md)

Quando un video viene creato usando la fotocamera del dispositivo, il file video viene archiviato nella libreria immagini del dispositivo. Questo articolo illustra come accedere al selettore immagini del dispositivo, per selezionare il video e quindi riprodurlo usando il lettore video.

## <a name="custom-video-transport-controlscustom-transportmd"></a>[Controlli di trasporto video personalizzati](custom-transport.md)

Anche se i lettori video in ogni piattaforma includono i propri controlli del trasporto, sotto forma di pulsanti **Riproduci** e **Sospendi**, è possibile eliminare la visualizzazione di questi pulsanti e aggiungere pulsanti personalizzati. Questo articolo spiega come fare.

## <a name="custom-video-positioningcustom-positioningmd"></a>[Posizionamento del video personalizzato](custom-positioning.md)

I lettori video per le diverse piattaforme dispongono di una barra di posizionamento, che visualizza l'avanzamento del video e consente di spostarsi avanti e indietro in una posizione specifica. Questo articolo illustra come sostituire la barra di posizionamento con un controllo personalizzato.





## <a name="related-links"></a>Collegamenti correlati

- [Demo lettore video (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
