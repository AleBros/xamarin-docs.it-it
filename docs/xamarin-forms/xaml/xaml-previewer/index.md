---
title: Visualizzatore anteprima XAML per xamarin. Forms
description: Questo articolo illustra come usare il Visualizzatore anteprima XAML per vedere i layout di xamarin. Forms viene eseguito il rendering durante la digitazione. Il Visualizzatore anteprima XAML è disponibile in Visual Studio 2019 e Visual Studio 2019 per Mac.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: db243a9c8dcb25f51bc7926a7aa239531e9c24f6
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58859011"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Visualizzatore anteprima XAML per xamarin. Forms

_Vedere i layout di xamarin. Forms viene eseguito il rendering durante la digitazione_

## <a name="overview"></a>Panoramica

Il Visualizzatore anteprima XAML viene illustrato l'aspetto della pagina XAML di xamarin. Forms in iOS e Android. Quando si apportano modifiche a di XAML, sarà possibile visualizzarli immediatamente anteprima insieme al codice. Il Visualizzatore anteprima XAML è disponibile in Visual Studio e Visual Studio per Mac.

## <a name="getting-started"></a>Per iniziare

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

È possibile aprire il Visualizzatore anteprima XAML facendo clic sulle frecce sul riquadro di visualizzazione di divisione. Se si desidera modificare il valore predefinito suddivisa il comportamento di visualizzazione, usare il **strumenti > Opzioni > Xamarin > Visualizzatore anteprima form** finestra di dialogo. In questa finestra di dialogo è possibile selezionare la visualizzazione documento predefinita e l'orientamento di divisione.

[![Xamarin.Forms Previewer options in Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "Xamarin.Forms Previewer options in Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Quando si apre un file XAML, si aprirà l'editor ingrandita o successiva per il Visualizzatore anteprima, in base alle impostazioni selezionate nel **strumenti > Opzioni > Xamarin > Visualizzatore anteprima form** finestra di dialogo. Tuttavia, la suddivisione può essere modificata per ogni file nella finestra dell'editor.

#### <a name="xaml-preview-controls"></a>Controlli di anteprima XAML

Scegliere se si desidera visualizzare il codice, il Visualizzatore anteprima XAML, o entrambi selezionando questi pulsanti di divisione riquadro di visualizzazione. Il pulsante centrale Scambia cosa lato il Visualizzatore anteprima e il codice si trovano in:

[![Xamarin. Form controlli Visualizzatore anteprima per passare da progettazione, origine e la visualizzazione suddivisa in Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "consente di controllare Visualizzatore anteprima di xamarin. Forms per passare da progettazione, origine e la visualizzazione suddivisa in Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

È possibile modificare la modalità schermo è suddividere orizzontalmente o verticalmente o comprimere completamente un riquadro:

[![Xamarin. Riquadro del Visualizzatore anteprima orientamento dei controlli Form in Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controlli orientamento del riquadro di anteprima di xamarin. Forms in Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

Il **Preview** pulsante viene visualizzato nell'editor, quando si apre una pagina XAML. Mostrare o nascondere il Visualizzatore anteprima premendo la **Preview** pulsante nell'angolo superiore destro di qualsiasi finestra del documento XAML:

[![Xamarin. Form Visualizzatore anteprima in Visual Studio per Mac](xaml-previewer-images/xamlp-list-sml.png "Visualizzatore anteprima di xamarin. Forms in Visual Studio per Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>Opzioni del Visualizzatore anteprima XAML

Le opzioni nella parte superiore del riquadro di anteprima sono:

* **Android** : Mostra la versione di Android della schermata
* **iOS** – Mostra la versione di iOS dello schermo (*Nota: Se si usa Visual Studio su Windows, è necessario essere [abbinato a un computer Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per usare questa modalità*)
* **Dispositivo** -elenco a discesa dei dispositivi Android o iOS, inclusi le dimensioni dello schermo e la risoluzione
* **Verticale (icona)** : Usa l'orientamento verticale per l'anteprima
* **Orizzontale (icona)** – USA orizzontale per l'anteprima

## <a name="detect-design-mode"></a>Modalità di progettazione di rilevamento

Il metodo statico [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) proprietà indica se l'applicazione viene eseguita nel Visualizzatore anteprima. Viene usato, è possibile specificare il codice che verrà eseguita solo quando l'applicazione è o non è in esecuzione nel Visualizzatore anteprima:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}

if (!DesignMode.IsDesignModeEnabled)
{
  // Don't run in the Previewer  
}
```

Questa proprietà è utile se si Inizializza una raccolta nel costruttore della pagina che non viene eseguita in fase di progettazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Controllare i problemi seguenti e il [forum di Xamarin](https://forums.xamarin.com/categories/xamarin-forms), se il Visualizzatore anteprima non funziona.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Visualizzatore anteprima XAML non è visualizzato o viene visualizzato un errore

* Può richiedere qualche minuto per il Visualizzatore anteprima per l'avvio, si noterà "l'inizializzazione di rendering" fino a quando non è pronto.
* Provare a chiudere e riaprire il file XAML.
* Verificare che il `App` classe ha un costruttore senza parametri.
* Controllare la versione di xamarin. Forms - deve essere almeno 3.6 di xamarin. Forms. È possibile aggiornare alla versione più recente di xamarin. Forms tramite NuGet.
* Controllo dell'installazione di JDK - visualizzazione in anteprima di Android richiede almeno [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Provare a uno qualsiasi di wrapping inizializzato classi della pagina C# code-behind in `if (!DesignMode.IsDesignModeEnabled)`.

### <a name="custom-controls-arent-rendering"></a>Non sono il rendering di controlli personalizzati

Provare a compilare il progetto. Il Visualizzatore anteprima Mostra classe base del controllo se non è possibile eseguire il rendering del controllo o se il creatore del controllo scelto esplicitamente di rendering fase di progettazione. Per altre informazioni, vedere [eseguire il rendering di controlli personalizzati nel Visualizzatore anteprima XAML](render-custom-controls.md).
