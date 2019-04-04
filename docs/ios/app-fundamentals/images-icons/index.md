---
title: Immagini e icone in xamarin. IOS
description: Questa sezione include una varietà di articoli che illustrano l'uso di immagini in un'app xamarin. IOS, ad esempio vengono utilizzate come icone, avviare le schermate o tra loro in controlli e fornendo le icone per i tipi di documento personalizzato.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f359da6b8744e03cfcbd77d48f5f77f216e828f8
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670947"
---
# <a name="images-and-icons-in-xamarinios"></a>Immagini e icone in xamarin. IOS

_Questa sezione include una varietà di articoli che illustrano l'uso di immagini in un'app xamarin. IOS, ad esempio vengono utilizzate come icone, avviare le schermate o tra loro in controlli e fornendo le icone per i tipi di documento personalizzato._

Esistono diversi modi quell'immagine vengono usati gli asset all'interno di un'app per iOS. Dalla visualizzazione semplicemente di un'immagine come parte dell'interfaccia utente dell'app, assegnarlo a un controllo dell'interfaccia utente, ad esempio un `UIButton` o `UIImageView`, per fornire le icone e schermate di avvio, xamarin. IOS rende più semplice aggiungere opere ideale per un'app per iOS nei modi seguenti: 

- **Immagini indipendente dalla risoluzione** : usare il supporto incorporato di iOS per l'utilizzo di immagini risoluzioni diversi dispositivi e dei tipi (iPhone, iPad e così via).
- **Set di immagini di catalogo di asset** -utilizzare **set di immagini di catalogo di Asset** per gestire e raggruppare tutte le versioni di un asset di immagine specificato richiesto da un'app.
- **Le immagini in iOS Designer** -usare iOS Designer per impostare le immagini per i controlli.
- **Le immagini nel codice** : usare il `UIImage` metodi della classe per caricare e usare gli asset di immagine e assegnarli ai controlli dell'interfaccia utente in C# codice.
- **Icona dell'applicazione** -definire l'icona dell'app richiesto da tutte le app iOS. Questa è l'icona che l'utente può toccare la schermata iniziale iOS per avviare l'app. Inoltre, questa icona viene usata dalla Game Center, se applicabile.
- **Spotlight icona** -definire l'icona dell'app in evidenza. Ogni volta che l'utente immette il nome di un'app in una ricerca di Spotlight, questa icona viene visualizzata.
- **Icona delle impostazioni predefinite** -definire l'app **impostazioni** icona. Se l'utente immette il **impostazioni** app nel dispositivo iOS, questa icona verrà visualizzata alla fine dell'elenco di impostazioni per l'app. 
- **Aprire schermate** -definire schermata di avvio dell'app. Dopo che l'utente tocca l'icona dell'app e prima che venga visualizzata la prima visualizzazione, verrà visualizzata una schermata vuota. Fortunatamente, iOS include il supporto per la visualizzazione di un'immagine al posto di schermo vuoto utilizzando uno Storyboard. 
- **Icona di iTunes** -forniscono un'icona di iTunes. Se si usa il metodo Ad-Hoc della distribuzione di un'app (sia per gli utenti aziendali o per la fase di beta testing su dispositivi reali), lo sviluppatore deve inoltre includere un 512x512 e un'immagine di 1024 x 1024 che verrà usata per rappresentare l'app in iTunes.
- **Documentare le icone** -usare un'immagine come icona per qualsiasi tipo di documento specifico che supporta o ne crea un'app xamarin. IOS.

Esistono diverse considerazioni che devono essere prese in considerazione durante la creazione di asset di immagini per un'app per iOS, nonché diverse posizioni in cui verranno utilizzate tali risorse. Ognuna di esse sono influisce sui non solo gli asset delle immagini quanti saranno necessari, ma come vengono creati tali asset. Gli argomenti seguenti descrivono i tipi di asset di immagini che verrà richiesto, come tali risorse sono inclusi nel bundle dell'applicazione e come gli asset delle immagini vengono usati per fornire le funzionalità richieste:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Questo articolo illustra tra cui un asset di immagine in un'app xamarin. IOS e visualizzazione di tale immagine usando codice c# o tramite l'assegnazione a un controllo in iOS Designer.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Icone delle applicazioni](~/ios/app-fundamentals/images-icons/app-icons.md)

Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin. IOS da usare come icona di App.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Icone alternative per le app](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple ha aggiunti numerosi miglioramenti a iOS 10.3 che consentono a un'app gestire la relativa icona:

 - `ApplicationIconBadgeNumber` : Ottiene o imposta il badge dell'icona dell'app in Springboard.
 - `SupportsAlternateIcons` -Se `true` l'app ha un set alternativo di icone.
 - `AlternateIconName` : Restituisce il nome dell'icona alternativa attualmente selezionata o `null` se si usa l'icona principale.
 - `SetAlternameIconName` -Usare questo metodo per cambiare l'icona dell'app per l'icona alternativo specificato.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Schermate di avvio](~/ios/app-fundamentals/images-icons/launch-screens.md)

Questo articolo illustra l'uso di un tipo speciale di Storyboard per fornire una schermata di avvio universale per tutte le dimensioni del dispositivo iOS e la risoluzione.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Tipi di documento personalizzati](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin. IOS da utilizzare come icona del tipo di documento personalizzato.



## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icona personalizzata e linee guida per la creazione di immagini](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
