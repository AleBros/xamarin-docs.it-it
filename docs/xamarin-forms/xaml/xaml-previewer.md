---
title: Visualizzatore anteprima XAML per xamarin. Forms
description: Questo articolo illustra come usare il Visualizzatore anteprima XAML per vedere i layout di xamarin. Forms viene eseguito il rendering durante la digitazione. Il Visualizzatore anteprima XAML è disponibile in Visual Studio 2017, Visual Studio per Mac e Visual Studio 2019 (anteprima).
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 02/04/2019
ms.openlocfilehash: 4258eca8e18229420ceb043a010c896137187653
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240383"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Visualizzatore anteprima XAML per xamarin. Forms

_Vedere i layout di xamarin. Forms viene eseguito il rendering durante la digitazione._

## <a name="requirements"></a>Requisiti

I progetti richiedono il pacchetto NuGet di xamarin. Forms più recente per il Visualizzatore anteprima XAML a funzionare. Richiede la visualizzazione in anteprima le app Android [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Altre informazioni, è il [note sulla versione](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Per iniziare

Il Visualizzatore anteprima XAML di xamarin. Forms Mostra un'anteprima specifiche della piattaforma del file XAML in tempo reale, mentre di modificarlo.

::: zone pivot="windows"

### <a name="visual-studio"></a>Visual Studio

Il Visualizzatore anteprima XAML è disponibile espandendo il riquadro di visualizzazione di divisione. Suddividere il comportamento di visualizzazione predefinito può essere controllato dal **strumenti > Opzioni > Xamarin > Visualizzatore anteprima form** finestra di dialogo. In questa finestra di dialogo è possibile selezionare la visualizzazione documento predefinita e l'orientamento di divisione.

[![Xamarin.Forms Previewer options in Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "Xamarin.Forms Previewer options in Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Quando si apre una pagina XAML, l'editor verrà suddiviso in base alle impostazioni selezionate nella **strumenti > Opzioni > Xamarin > Visualizzatore anteprima form** finestra di dialogo. Tuttavia, la suddivisione può essere modificata per ogni file nella finestra dell'editor.

#### <a name="xaml-preview-controls"></a>Controlli di anteprima XAML

La parte superiore della finestra dell'editor è disponibili pulsanti per selezionare il riquadro è in uso, con il pulsante in alto passaggio nel riquadro di progettazione e il pulsante in basso passaggio al riquadro del codice sorgente. Il pulsante centrale inverte l'ordine di riquadro.

[![Consente di controllare Visualizzatore anteprima di xamarin. Forms per passare da progettazione, origine e la visualizzazione suddivisa in Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "consente di controllare Visualizzatore anteprima di xamarin. Forms per passare da progettazione, origine e la visualizzazione suddivisa in Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

La parte inferiore della finestra dell'editor è disponibili pulsanti orizzontalmente e verticalmente dividere i riquadri e per espandere o comprimere il riquadro secondario corrente.

[![I controlli di orientamento riquadro Visualizzatore anteprima di xamarin. Forms in Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controlli orientamento del riquadro di anteprima di xamarin. Forms in Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

Il **Preview** pulsante viene visualizzato nell'editor, quando si apre una pagina XAML. Nel riquadro di anteprima può essere visualizzato o nascosto premendo la **Preview** pulsante nell'angolo superiore destro di qualsiasi finestra del documento XAML:

[![Visualizzatore anteprima di xamarin. Forms in Visual Studio per Mac](xaml-previewer-images/xamlp-list-sml.png "Visualizzatore anteprima di xamarin. Forms in Visual Studio per Mac")](xaml-previewer-images/xamlp-list.png#lightbox)

::: zone-end
::: zone pivot="win-dev16"

### <a name="visual-studio-2019-preview"></a>Visual Studio 2019 (Preview)

Il Visualizzatore anteprima XAML è disponibile espandendo il riquadro di visualizzazione di divisione. Suddividere il comportamento di visualizzazione predefinito può essere controllato dal **strumenti > Opzioni > Xamarin > Visualizzatore anteprima form** finestra di dialogo. In questa finestra di dialogo è possibile selezionare la visualizzazione documento predefinita e l'orientamento di divisione.

[![Xamarin.Forms Previewer options in Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "Xamarin.Forms Previewer options in Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Quando si apre una pagina XAML, l'editor verrà suddiviso in base alle impostazioni selezionate nella **strumenti > Opzioni > Xamarin > Visualizzatore anteprima form** finestra di dialogo. Tuttavia, la suddivisione può essere modificata per ogni file nella finestra dell'editor.

#### <a name="xaml-preview-controls"></a>Controlli di anteprima XAML

La parte superiore della finestra dell'editor è disponibili pulsanti per selezionare il riquadro è in uso, con il pulsante in alto passaggio nel riquadro di progettazione e il pulsante in basso passaggio al riquadro del codice sorgente. Il pulsante centrale inverte l'ordine di riquadro.

[![Consente di controllare Visualizzatore anteprima di xamarin. Forms per passare da progettazione, origine e la visualizzazione suddivisa in Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "consente di controllare Visualizzatore anteprima di xamarin. Forms per passare da progettazione, origine e la visualizzazione suddivisa in Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

La parte inferiore della finestra dell'editor è disponibili pulsanti orizzontalmente e verticalmente dividere i riquadri e per espandere o comprimere il riquadro secondario corrente.

[![I controlli di orientamento riquadro Visualizzatore anteprima di xamarin. Forms in Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "controlli orientamento del riquadro di anteprima di xamarin. Forms in Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

::: zone-end

## <a name="xaml-previewer-options"></a>Opzioni del Visualizzatore anteprima XAML

Le opzioni nella parte superiore del riquadro di anteprima sono:

* **Telefono** : anteprima in una schermata di phone ridimensionato
* **Tablet** -anteprima in una schermata di tablet di dimensioni
* **Android** : Mostra la versione di Android della schermata
* **iOS** – Mostra la versione di iOS dello schermo (*Nota: Se si usa Visual Studio su Windows, è necessario essere [abbinato a un computer Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per usare questa modalità*)
* **Verticale (icona)** : Usa l'orientamento verticale per l'anteprima
* **Orizzontale (icona)** – USA orizzontale per l'anteprima

::: zone pivot="win-dev16"

## <a name="device-drop-down-menu"></a>Menu di scelta rapida di dispositivo

In Visual Studio 2019 (anteprima), è possibile selezionare un dispositivo per visualizzare in anteprima in un elenco di elenco a discesa dei dispositivi Android o iOS. Questo elenco a discesa sostituisce le opzioni "Phone" e "Tablet PC". Durante l'anteprima iOS, elenco a discesa visualizzerà i dispositivi iPhone e iPad. Durante l'anteprima Android, verrà visualizzato l'elenco a discesa diversi telefoni e Tablet Android. Entrambi gli elenchi includono le dimensioni dello schermo e risoluzioni dello schermo.

::: zone-end

## <a name="detect-design-mode"></a>Modalità di progettazione di rilevamento

Il metodo statico [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) proprietà può essere esaminata per determinare se l'applicazione è in esecuzione nel Visualizzatore anteprima. In questo modo è possibile specificare il codice che verrà eseguita solo quando l'applicazione è in esecuzione nel Visualizzatore anteprima:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

::: zone pivot="win-dev16"

## <a name="enable-design-time-rendering-for-custom-controls"></a>Abilitare il rendering di fase di progettazione per i controlli personalizzati

Controlli personalizzati sono necessario scegliere esplicitamente di rendering in tempo da visualizzare nel Visualizzatore anteprima, indipendentemente dal fatto se il controllo si trova nel progetto o viene importato da una libreria di progettazione. Questa operazione può essere eseguita aggiungendo il [ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute) alla classe del controllo:

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Un esempio di questo può essere visualizzato nel [classe di base di ImageCirclePlugin di James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs).

::: zone-end

## <a name="add-design-time-data"></a>Aggiungere dati design-time

Alcuni layout potrebbe essere difficile visualizzare senza dati associati a controlli dell'interfaccia utente. Per rendere più utile all'anteprima, assegnare alcuni dati statici per i controlli da impostare come hardcoded un contesto di associazione (sia nel code-behind o tramite XAML).

Fare riferimento a di James Montemagno [post di blog sull'aggiunta di dati design-time](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) per informazioni su come associare a un elemento ViewModel statiche in XAML.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Controllare i problemi seguenti e il [forum di Xamarin](https://forums.xamarin.com/categories/xamarin-forms), se si verificano problemi.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Visualizzatore anteprima XAML non è visualizzato o viene visualizzato un errore

Verificare quanto segue:

* L'agente di progettazione deve essere configurato nel momento in che anteprima un file XAML, un indicatore di stato verrà visualizzati nel Visualizzatore anteprima, insieme ai messaggi di stato di avanzamento, fino a quando non si è pronto.
* Provare a chiudere e riaprire il file XAML.
* Verificare che il `App` classe ha un costruttore senza parametri.

::: zone pivot="win-dev16"

### <a name="custom-controls-arent-rendering"></a>Non sono il rendering di controlli personalizzati

Assicurarsi che il progetto viene compilato. Se il Visualizzatore anteprima non è possibile eseguire il rendering del controllo senza un errore o il creatore del controllo è stata non opt-in per il rendering in fase di progettazione, il Visualizzatore anteprima Mostra classe base del controllo.

::: zone-end
