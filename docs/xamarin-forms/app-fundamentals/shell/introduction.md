---
title: Introduzione alla shell Xamarin.Forms
description: La shell Xamarin.Forms fornisce le principali funzionalità richieste dalla maggior parte delle applicazioni, tra cui un'esperienza utente di navigazione comune, uno schema di navigazione basato su URI e un gestore di ricerca integrato.
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2019
ms.openlocfilehash: 3e63a580bbdb1c220d44b100725cdc8ce387b405
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696523"
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
> Le applicazioni esistenti possono adottare la shell e trarre vantaggio immediatamente dai miglioramenti per la navigazione, le prestazioni e l'estensibilità.

## <a name="platform-support"></a>Supporto per piattaforme

La shell di Novell. Forms è completamente disponibile in iOS e Android, ma solo parzialmente disponibile nel piattaforma UWP (Universal Windows Platform) (UWP). Inoltre, la shell è attualmente sperimentale in UWP e può essere usata solo aggiungendo la riga di codice seguente alla classe `App` nel progetto UWP, prima di chiamare `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

## <a name="shell-navigation-experience"></a>Esperienza di navigazione nella shell

La shell offre una solida esperienza di navigazione, basata su riquadri a comparsa e schede. Il livello principale per lo spostamento in un'applicazione shell è un riquadro a comparsa o una barra di schede inferiore, a seconda dei requisiti dell'applicazione per gli spostamenti. L'esempio seguente illustra un'applicazione in cui il livello principale per lo spostamento è un riquadro a comparsa:

[![Screenshot di un riquadro a comparsa della shell, in iOS e Android](introduction-images/flyout.png "Riquadro a comparsa Shell")](introduction-images/flyout-large.png#lightbox "Riquadro a comparsa Shell")

Selezionando un elemento del riquadro a comparsa, la scheda inferiore che rappresenta l'elemento viene selezionata e visualizzata:

[![Screenshot delle schede in basso della shell, in iOS e Android](introduction-images/monkeys.png "Schede in basso Shell")](introduction-images/monkeys-large.png#lightbox "Schede in basso Shell")

> [!NOTE]
> Quando il riquadro a comparsa non è aperto, la barra delle schede inferiore può essere considerata il livello di spostamento principale nell'applicazione.

Ogni scheda visualizza un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Se tuttavia una scheda inferiore contiene più di una pagina, è possibile spostarsi tra le pagine tramite la barra delle schede superiore:

[![Screenshot delle schede principali della shell, in iOS e Android](introduction-images/cats.png "Schede principali della shell")](introduction-images/cats-large.png#lightbox "Schede principali della shell")

All'interno di ogni scheda, è possibile spostarsi ad altri oggetti [`ContentPage`](xref:Xamarin.Forms.ContentPage):

[![Screenshot della navigazione delle pagine della shell, in iOS e Android](introduction-images/cat-details.png "Esplorazione delle app shell")](introduction-images/cat-details-large.png#lightbox "Esplorazione delle app shell")

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
