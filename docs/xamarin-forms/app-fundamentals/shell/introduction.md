---
title: Introduzione alla shell Xamarin.Forms
description: La shell Xamarin.Forms fornisce le principali funzionalità richieste dalla maggior parte delle applicazioni, tra cui un'esperienza utente di navigazione comune, uno schema di navigazione basato su URI e un gestore di ricerca integrato.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
ms.openlocfilehash: dd1dc9b679a46dc082de1fe9b3c5f10b6757c0d8
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739278"
---
# <a name="xamarinforms-shell-introduction"></a>Introduzione alla shell Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

La shell Xamarin.Forms riduce la complessità dello sviluppo di applicazioni per dispositivi mobili offrendo le principali funzionalità richieste dalla maggior parte delle applicazioni per dispositivi mobili, tra cui:

- Un'unica posizione per descrivere la gerarchia visiva di un'applicazione.
- Un'esperienza utente di navigazione comune.
- Uno schema di navigazione basato su URI che consente di passare a qualsiasi pagina nell'applicazione.
- Un gestore integrato per la ricerca.

Le applicazioni shell consentono inoltre una velocità di rendering maggiore e un consumo di memoria ridotto.

> [!IMPORTANT]
> La shell Xamarin.Forms è disponibile solo in iOS e Android. Le applicazioni Android e iOS esistenti possono adottare Shell usufruendo immediatamente dei miglioramenti in termini di navigazione, prestazioni ed estendibilità.

## <a name="shell-navigation-experience"></a>Esperienza di navigazione nella shell

La shell offre una solida esperienza di navigazione, basata su riquadri a comparsa e schede. Il livello principale per lo spostamento in un'applicazione shell è un riquadro a comparsa o una barra di schede inferiore, a seconda dei requisiti dell'applicazione per gli spostamenti. L'esempio seguente illustra un'applicazione in cui il livello principale per lo spostamento è un riquadro a comparsa:

[![Screenshot di un riquadro a comparsa della shell, in iOS e Android](introduction-images/flyout.png "Riquadro a comparsa della shell")](introduction-images/flyout-large.png#lightbox "Riquadro a comparsa della shell")

Selezionando un elemento del riquadro a comparsa, la scheda inferiore che rappresenta l'elemento viene selezionata e visualizzata:

[![Screenshot delle schede inferiori della shell, in iOS e Android](introduction-images/monkeys.png "Schede inferiori della shell")](introduction-images/monkeys-large.png#lightbox "Schede inferiori della shell")

> [!NOTE]
> Quando il riquadro a comparsa non è aperto, la barra delle schede inferiore può essere considerata il livello di spostamento principale nell'applicazione.

Ogni scheda visualizza un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Se tuttavia una scheda inferiore contiene più di una pagina, è possibile spostarsi tra le pagine tramite la barra delle schede superiore:

[![Screenshot delle schede superiori della shell, in iOS e Android](introduction-images/cats.png "Schede superiori della shell")](introduction-images/cats-large.png#lightbox "Schede superiori della shell")

All'interno di ogni scheda, è possibile spostarsi ad altri oggetti [`ContentPage`](xref:Xamarin.Forms.ContentPage):

[![Screenshot della navigazione tra pagine della shell, in iOS e Android](introduction-images/cat-details.png "Navigazione nell'app shell")](introduction-images/cat-details-large.png#lightbox "Navigazione nell'app shell")

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
