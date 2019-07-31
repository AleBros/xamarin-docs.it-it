---
title: Immagini e icone in Novell. iOS
description: Questa sezione include una serie di articoli che illustrano l'uso delle immagini in un'app Novell. iOS, ad esempio l'uso come icone, le schermate di avvio o la loro inclusione nei controlli e la visualizzazione di icone per i tipi di documento personalizzati.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 443cdb5028964e64aeb831f8d706bfdb99f637b2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654449"
---
# <a name="images-and-icons-in-xamarinios"></a>Immagini e icone in Novell. iOS

_Questa sezione include una serie di articoli che illustrano l'uso delle immagini in un'app Novell. iOS, ad esempio l'uso come icone, le schermate di avvio o la loro inclusione nei controlli e la visualizzazione di icone per i tipi di documento personalizzati._

Sono disponibili diversi modi per usare le risorse dell'immagine all'interno di un'app iOS. Dalla semplice visualizzazione di un'immagine come parte dell'interfaccia utente di un'app a, assegnando tale immagine a un controllo dell' `UIButton` interfaccia `UIImageView`utente, ad esempio o, per fornire icone e schermate di avvio, Novell. iOS semplifica l'aggiunta di elementi grafici eccezionali a un'app iOS nei modi seguenti: 

- **Immagini indipendenti dalla risoluzione** : usare il supporto incorporato di iOS per l'uso di immagini in diversi tipi e risoluzioni di dispositivi (iPhone, iPad e così via).
- **Set di immagini del catalogo asset** : usare i **set di immagini del catalogo asset** per gestire e raggruppare tutte le versioni di un asset immagine specifico richiesto da un'app.
- **Immagini in iOS designer** : usare la finestra di progettazione iOS per impostare immagini per i controlli.
- **Immagini nel codice** : usare i `UIImage` metodi della classe per caricare e usare asset di immagini e assegnarli ai controlli dell'interfaccia C# utente nel codice.
- **Icona dell'applicazione** : definire l'icona dell'app richiesta da ogni app iOS. Questa è l'icona che l'utente toccherà dalla schermata iniziale di iOS per avviare l'app. Questa icona viene inoltre utilizzata da Game Center, se applicabile.
- **Icona in evidenza** : definire l'icona in primo piano dell'app. Ogni volta che l'utente immette il nome di un'app in una ricerca Spotlight, viene visualizzata questa icona.
- **Icona delle impostazioni** : definire l'icona **delle impostazioni** dell'app. Se l'utente immette l'app **Settings** sul dispositivo iOS, questa icona verrà visualizzata alla fine dell'elenco delle impostazioni per l'app. 
- **Schermate di avvio** : definire la schermata di avvio dell'app. Quando l'utente tocca l'icona dell'app e prima che venga visualizzata la prima visualizzazione, verrà visualizzata una schermata vuota. Fortunatamente, iOS include il supporto per la visualizzazione di un'immagine al posto della schermata vuota usando uno storyboard. 
- **icona iTunes** : specificare un'icona iTune. Se si usa il metodo ad hoc per la distribuzione di un'app (per gli utenti aziendali o per il test beta nei dispositivi reali), lo sviluppatore deve anche includere un 512x512 e un'immagine 1024x1024 che verranno usati per rappresentare l'app in iTunes.
- **Icone del documento** : usare un'immagine come icona per qualsiasi tipo di documento specifico supportato o creato da un'app Novell. iOS.

Quando si creano asset immagine per un'app iOS, è necessario tenere presenti diverse considerazioni, oltre a diverse posizioni in cui verranno usate tali asset. Ognuno di questi ha un effetto su non solo sul numero di asset immagine necessari, ma sulla modalità di creazione degli asset. Gli argomenti seguenti illustrano i tipi di asset di immagini che saranno necessari, il modo in cui tali asset vengono inclusi nel bundle dell'applicazione e il modo in cui vengono utilizzate le risorse dell'immagine per fornire le funzionalità necessarie:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Visualizzazione di un'immagine](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Questo articolo illustra l'inclusione di un asset immagine in un'app Novell. iOS e la visualizzazione dell' C# immagine usando il codice o assegnando il codice a un controllo in iOS designer.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Icone delle applicazioni](~/ios/app-fundamentals/images-icons/app-icons.md)

Questo articolo illustra l'inclusione e la gestione di un asset immagine in un'app Novell. iOS da usare come icona dell'app.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Icone alternative per le app](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple ha aggiunto diversi miglioramenti a iOS 10,3 che consentono a un'app di gestire la relativa icona:

- `ApplicationIconBadgeNumber`: Ottiene o imposta la notifica dell'icona dell'app nel Springboard.
- `SupportsAlternateIcons`-Se `true` l'app dispone di un set alternativo di icone.
- `AlternateIconName`: Restituisce il nome dell'icona alternativa attualmente selezionata o `null` se si utilizza l'icona primaria.
- `SetAlternameIconName`-Usare questo metodo per impostare l'icona dell'app sull'icona alternativa specificata.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Schermate di avvio](~/ios/app-fundamentals/images-icons/launch-screens.md)

Questo articolo illustra l'uso di un tipo speciale di storyboard per fornire una schermata di avvio universale per ogni dimensione e risoluzione del dispositivo iOS.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Tipi di documento personalizzati](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Questo articolo illustra l'inclusione e la gestione di un asset immagine in un'app Novell. iOS da usare come icona di tipo di documento personalizzato.



## <a name="related-links"></a>Collegamenti correlati

- [Uso delle immagini (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Linee guida per la creazione di icone e immagini personalizzate](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
