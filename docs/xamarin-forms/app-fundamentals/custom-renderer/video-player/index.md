---
title: Implementazione di un lettore video
ms.topic: article
ms.prod: xamarin
ms.assetid: 0CE9BEE7-4F81-4A00-B9B3-5E2535CD3050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: a889be5ee31f667117d2c36859e667980f0e6610
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="implementing-a-video-player"></a>Implementazione di un lettore video

Talvolta è utile per riprodurre i file video in un'applicazione di xamarin. Forms. Questa serie di articoli viene illustrato come scrivere un renderer personalizzato per iOS, Android e la piattaforma UWP (Universal Windows) per una classe di xamarin. Forms denominata `VideoPlayer`.

Nel [ **VideoPlayerDemos** ](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/) di esempio, tutti i file che implementano e supportano `VideoPlayer` si trovano in cartelle denominate `FormsVideoLibrary` e identificato con lo spazio dei nomi `FormsVideoLibrary` o spazi dei nomi che iniziano `FormsVideoLibrary`. Questa organizzazione e denominazione deve rendono più semplice copiare i file video player nella propria soluzione xamarin. Forms.

`VideoPlayer` possibile riprodurre i file video da tre tipi di origini:

- Internet tramite un URL
- Una risorsa incorporata nell'applicazione della piattaforma
- Catalogo video del dispositivo

Lettori video richiedono *controlli di trasporto*, quali sono i pulsanti per la riproduzione e la sospensione del video, e un posizionamento barre che mostra lo stato di avanzamento tramite il video e consente all'utente di passare rapidamente a un percorso diverso. `VideoPlayer` può utilizzare entrambi i controlli di trasporto e posizionamento barra fornita per la piattaforma (come illustrato di seguito), oppure è possibile fornire controlli di trasporto personalizzato e una barra di posizionamento. Di seguito è riportato il programma in esecuzione in iOS, Android e la piattaforma Windows universale:

[![Riprodurre Video Web](web-videos-images/playwebvideo-small.png "riprodurre Video Web")](web-videos-images/playwebvideo-large.png#lightbox "riprodurre Video Web")

Naturalmente, è possibile attivare il telefono lateralmente per una visualizzazione più grande.

Un lettore video più sofisticato avrebbe alcune funzionalità aggiuntive, ad esempio controllo volume, un meccanismo per interrompere il video quando arriva una telefonata e un modo di mantenere attiva la schermata durante la riproduzione.

La seguente serie di articoli progressivamente Mostra la modalità di compilazione il renderer di piattaforma e classi di supporto:

## <a name="creating-the-platform-video-playersplayer-creationmd"></a>[Creazione di lettori video piattaforma](player-creation.md)

Ogni piattaforma richiede un `VideoPlayerRenderer` classe che crea e gestisce un controllo di lettore video supportato dalla piattaforma. Questo articolo illustra la struttura del renderer di classi e come creare i lettori.

## <a name="playing-a-web-videoweb-videosmd"></a>[Riproduzione di un video Web](web-videos.md)

L'origine più comune di video per un lettore video è probabilmente Internet. In questo articolo viene descritto come un video Web può essere fatto riferimento e utilizzato come origine per il lettore video.

## <a name="binding-video-sources-to-the-playersource-bindingsmd"></a>[Associazione origini video al lettore](source-bindings.md)

Questo articolo viene utilizzato un `ListView` per presentare una raccolta di video da riprodurre. Un programma mostra come il file code-behind possibile impostare l'origine video del lettore video, ma un programma secondo come è possibile utilizzare l'associazione tra il `ListView` e il lettore video.

## <a name="loading-application-resource-videosloading-resourcesmd"></a>[Il caricamento di applicazione risorse video](loading-resources.md)

Video possono essere incorporati come risorse nei progetti di piattaforma. In questo articolo viene illustrato come archiviare tali risorse e caricarli in un secondo momento nel programma per essere riprodotte dal lettore video.

## <a name="accessing-the-devices-video-libraryaccessing-librarymd"></a>[L'accesso a Catalogo video del dispositivo](accessing-library.md)

Quando viene creato un video con la fotocamera del dispositivo, il file video viene archiviato nella libreria di immagini del dispositivo. In questo articolo viene illustrato come accedere selettore immagine del dispositivo per selezionare il video e quindi riprodurla utilizzando il lettore video.

## <a name="custom-video-transport-controlscustom-transportmd"></a>[Controlli di trasporto personalizzato di video](custom-transport.md)

Sebbene i lettori video in ciascuna piattaforma di forniscono i propri controlli di trasporto sotto forma di pulsanti per **riprodurre** e **pausa**, è possibile disattivare la visualizzazione dei pulsanti e fornire la propria. In questo articolo illustra come.

## <a name="custom-video-positioningcustom-positioningmd"></a>[Il posizionamento video personalizzato](custom-positioning.md)

Ognuno dei lettori video piattaforma è un indicatore di posizione che mostra lo stato di avanzamento del video e consente di spostarsi avanti e indietro in una posizione specifica. In questo articolo viene illustrato come è possibile sostituire tale barra posizione con un controllo personalizzato.





## <a name="related-links"></a>Collegamenti correlati

- [Demo di lettore video (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/VideoPlayerDemos/)
