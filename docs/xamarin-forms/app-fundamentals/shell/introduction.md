---
title: Xamarin.FormsIntroduzione alla shell
description: Xamarin.FormsShell fornisce le funzionalità di base richieste dalla maggior parte delle applicazioni, tra cui un'esperienza utente di navigazione comune, uno schema di navigazione basato su URI e un gestore di ricerca integrato.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 29a99161ff2ef2d71b6c803db994522bfe80ed03
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138736"
---
# <a name="xamarinforms-shell-introduction"></a>Xamarin.FormsIntroduzione alla shell

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Xamarin.FormsShell riduce la complessità dello sviluppo di applicazioni per dispositivi mobili fornendo le funzionalità fondamentali necessarie per la maggior parte delle applicazioni per dispositivi mobili, tra cui:

- Un'unica posizione per descrivere la gerarchia visiva di un'applicazione.
- Un'esperienza utente di navigazione comune.
- Uno schema di navigazione basato su URI che consente di passare a qualsiasi pagina nell'applicazione.
- Un gestore integrato per la ricerca.

Le applicazioni shell consentono inoltre una velocità di rendering maggiore e un consumo di memoria ridotto.

> [!IMPORTANT]
> Le applicazioni esistenti possono adottare Shell usufruendo immediatamente dei miglioramenti in termini di navigazione, prestazioni ed estendibilità.

## <a name="platform-support"></a>Piattaforme supportate

Xamarin.FormsShell è completamente disponibile in iOS e Android, ma solo parzialmente disponibile nel piattaforma UWP (Universal Windows Platform) (UWP). Inoltre, la shell è attualmente in fase sperimentale in UWP e può essere usata solo aggiungendo la riga di codice seguente alla classe `App` nel progetto UWP, prima di chiamare `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

Per informazioni su come aggiungere un progetto UWP a una Xamarin.Forms soluzione, vedere [configurare i progetti di Windows](~/xamarin-forms/platform/windows/installation/index.md).

## <a name="shell-navigation-experience"></a>Esperienza di navigazione nella shell

La shell offre una solida esperienza di navigazione, basata su riquadri a comparsa e schede. Il livello principale per lo spostamento in un'applicazione shell è un riquadro a comparsa o una barra di schede inferiore, a seconda dei requisiti dell'applicazione per gli spostamenti. L'esempio seguente illustra un'applicazione in cui il livello principale per lo spostamento è un riquadro a comparsa:

[![Screenshot di un riquadro a comparsa della shell, in iOS e Android](introduction-images/flyout.png "Riquadro a comparsa della shell")](introduction-images/flyout-large.png#lightbox "Riquadro a comparsa della shell")

Selezionando un elemento del riquadro a comparsa, la scheda inferiore che rappresenta l'elemento viene selezionata e visualizzata:

[![Screenshot delle schede inferiori della shell, in iOS e Android](introduction-images/monkeys.png "Schede inferiori della shell")](introduction-images/monkeys-large.png#lightbox "Schede inferiori della shell")

> [!NOTE]
> Quando il riquadro a comparsa non è aperto, la barra delle schede inferiore può essere considerata il livello di spostamento principale nell'applicazione.

Ogni scheda Visualizza un [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Se tuttavia una scheda inferiore contiene più di una pagina, è possibile spostarsi tra le pagine tramite la barra delle schede superiore:

[![Screenshot delle schede superiori della shell, in iOS e Android](introduction-images/cats.png "Schede superiori della shell")](introduction-images/cats-large.png#lightbox "Schede superiori della shell")

All'interno di ogni scheda [`ContentPage`](xref:Xamarin.Forms.ContentPage) è possibile passare a oggetti aggiuntivi:

[![Screenshot della navigazione tra le pagine della shell, in iOS e Android](introduction-images/cat-details.png "Esplorazione delle app della shell")](introduction-images/cat-details-large.png#lightbox "Esplorazione delle app della shell")

## <a name="related-links"></a>Collegamenti correlati

- [Xaminals (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
