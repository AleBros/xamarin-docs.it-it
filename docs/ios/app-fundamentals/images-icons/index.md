---
title: Le immagini e icone in xamarin. IOS
description: In questa sezione include una varietà di articoli che illustrano l'utilizzo di immagini in un'app xamarin. IOS, ad esempio vengono utilizzate come icone, avviare schermate o tra essi nei controlli e fornendo le icone per i tipi di documento personalizzato.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 444e3cbd88dfd3ff50153d858367ecd0310d240a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784052"
---
# <a name="images-and-icons-in-xamarinios"></a>Le immagini e icone in xamarin. IOS

_In questa sezione include una varietà di articoli che illustrano l'utilizzo di immagini in un'app xamarin. IOS, ad esempio vengono utilizzate come icone, avviare schermate o tra essi nei controlli e fornendo le icone per i tipi di documento personalizzato._

Esistono diversi modi quell'immagine asset vengono utilizzati all'interno di un'app iOS. È sufficiente visualizzare un'immagine come parte dell'interfaccia utente di un'app, l'assegnazione a un controllo dell'interfaccia utente, ad esempio un `UIButton` o `UIImageView`, per fornire le icone e schermate di avvio, xamarin è semplice aggiungere grafica ottima in un'app iOS nei modi seguenti: 

- **Immagini indipendente dalla risoluzione** : utilizzare il supporto incorporato di iOS per l'utilizzo di immagini risoluzioni diversi dispositivi e dei tipi (iPhone, iPad e così via).
- **Set di immagini di catalogo di asset** -utilizzo **insiemi di immagini di catalogo di Asset** di gestire e raggruppare tutte le versioni di una risorsa immagine specificato richiesta da un'app.
- **Le immagini nella finestra di progettazione iOS** -utilizzare la finestra di progettazione iOS per impostare le immagini per i controlli.
- **Le immagini nel codice** : utilizzo di `UIImage` metodi della classe per caricare e lavorare con le risorse immagine e assegnarli ai controlli dell'interfaccia utente nel codice c#.
- **Icona dell'applicazione** -definire l'icona dell'app richiesto da tutte le app iOS. Questa è l'icona che l'utente verrà toccare dalla schermata iniziale di iOS per avviare l'app. Inoltre, questa icona viene utilizzata da Game Center, se applicabile.
- **Icona riflettore** -definire icona in primo piano dell'app. Ogni volta che l'utente immette il nome di un'app in una ricerca Spotlight, questa icona viene visualizzata.
- **Icona impostazioni** -definire l'app **impostazioni** icona. Se l'utente immette il **impostazioni** app sul proprio dispositivo iOS, questa icona verrà visualizzata alla fine dell'elenco delle impostazioni per l'app. 
- **Aprire schermate** -definire la schermata di avvio dell'app. Dopo che l'utente tocca l'icona dell'app e prima che venga visualizzata la prima vista, verrà visualizzata una schermata vuota. Fortunatamente, iOS include il supporto per la visualizzazione di un'immagine al posto di schermata vuota utilizzando uno Storyboard. 
- **Icona iTunes** -forniscono un'icona iTune. Se tramite il metodo Ad Hoc di fornire un'app (per gli utenti aziendali o di test della versione beta su dispositivi), lo sviluppatore deve inoltre includere un 512 x 512 e un'immagine di 1024 x 1024 che verrà utilizzata per rappresentare l'app in iTunes.
- **Documentare le icone** -usare un'immagine come un'icona per qualsiasi tipo di documento specifico che supporta o ne crea un'app xamarin. IOS.

Ci sono diverse considerazioni che devono essere prese in considerazione durante la creazione di asset di immagini per un'app iOS, nonché diverse posizioni in cui verranno utilizzati tali risorse. Ognuno di questi hanno un effetto non solo sull'asset immagine quanti saranno necessarie, ma le modalità di creazione delle risorse. Gli argomenti seguenti includono i tipi di asset di immagini che verrà richiesto, come tali risorse sono inclusi nel bundle dell'applicazione e come gli asset di immagini vengono utilizzati per fornire la funzionalità richiesta:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Questo articolo descrive incluso un asset di immagine in un'app xamarin. IOS e la visualizzazione di quell'immagine usando codice c# o tramite l'assegnazione a un controllo nella finestra di progettazione iOS.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Icone delle applicazioni](~/ios/app-fundamentals/images-icons/app-icons.md)

Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine in un'app xamarin da utilizzare come icona dell'App.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Icone alternative per le app](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple ha aggiunto numerosi miglioramenti a iOS 10.3 che consentono di gestire la relativa icona di un'app:

 - `ApplicationIconBadgeNumber` -Ottiene o imposta la chiave per il badge dell'icona di app.
 - `SupportsAlternateIcons` -Se `true` l'applicazione dispone di un set alternativo di icone.
 - `AlternateIconName` : Restituisce il nome dell'icona alternativo attualmente selezionata o `null` se si utilizza l'icona primario.
 - `SetAlternameIconName` : Utilizzare questo metodo per passare l'icona dell'app dell'icona alternativo specificato.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Schermate di avvio](~/ios/app-fundamentals/images-icons/launch-screens.md)

In questo articolo viene illustrato l'utilizzo di un tipo speciale di Storyboard per fornire una schermata di avvio universale per tutte le dimensioni del dispositivo iOS e la risoluzione.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Tipi di documento personalizzati](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Questo articolo viene illustrato come inclusi e la gestione di un asset di immagine da utilizzare come icona del tipo di documento personalizzato in un'app xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di immagini (esempio)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icona personalizzata e linee guida per la creazione di immagini](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
