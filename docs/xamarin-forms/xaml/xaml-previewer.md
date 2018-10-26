---
title: Visualizzatore anteprima XAML per xamarin. Forms
description: Questo articolo illustra come usare il Visualizzatore anteprima XAML per vedere i layout di xamarin. Forms viene eseguito il rendering durante la digitazione. Il Visualizzatore anteprima XAML è disponibile in Visual Studio 2017 e Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/31/2018
ms.openlocfilehash: def7a7a3bdd9e165252c5ad1928b89ec654e5d74
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108086"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Visualizzatore anteprima XAML per xamarin. Forms

_Vedere i layout di xamarin. Forms viene eseguito il rendering durante la digitazione._

## <a name="requirements"></a>Requisiti

I progetti richiedono il pacchetto NuGet di xamarin. Forms più recente per il Visualizzatore anteprima XAML a funzionare. Richiede la visualizzazione in anteprima le app Android [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Altre informazioni, è il [note sulla versione](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Introduzione

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il Visualizzatore anteprima XAML è abilitata per impostazione predefinita e può essere controllato dal **strumenti > Opzioni > Xamarin > Visualizzatore anteprima form** finestra di dialogo. In questa finestra di dialogo è possibile selezionare la visualizzazione documento predefinita e l'orientamento di divisione.

[![Anteprima di controllo ListView in Visual Studio](xaml-previewer-images/xamlp-options-vs.png "form Visualizzatore anteprima opzioni in Visual Studio")](xaml-previewer-images/xamlp-options-vs.png#lightbox "form Visualizzatore anteprima opzioni in Visual Studio")

Quando l'apertura di una pagina XAML suddividerà l'editor basato sulle impostazioni selezionate nella **strumenti > Opzioni > Xamarin > Form Visualizzatore anteprima** finestra di dialogo. Tuttavia, queste preferenze possono essere modificate nella finestra dell'editor.

## <a name="xaml-preview-controls"></a>Controlli di anteprima XAML

La parte superiore della finestra dell'editor è disponibili pulsanti per selezionare il riquadro è in uso, con il pulsante in alto passaggio nel riquadro di progettazione e il pulsante in basso passaggio al riquadro del codice sorgente. Il pulsante centrale inverte l'ordine di riquadro.

[![Anteprima di controllo ListView in Visual Studio](xaml-previewer-images/xamlp-controls-vs.png "form Visualizzatore anteprima riquadro controlli in Visual Studio")](xaml-previewer-images/xamlp-controls-vs.png#lightbox "riquadro Visualizzatore anteprima form controlli in Visual Studio")

La parte inferiore della finestra dell'editor è disponibili pulsanti orizzontalmente e verticalmente dividere i riquadri e per espandere o comprimere il riquadro secondario corrente.

[![Anteprima di controllo ListView in Visual Studio](xaml-previewer-images/xamlp-controls2-vs.png "form Visualizzatore anteprima riquadro controlli in Visual Studio")](xaml-previewer-images/xamlp-controls2-vs.png#lightbox "riquadro Visualizzatore anteprima form controlli in Visual Studio")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Il **Preview** pulsante viene visualizzato nell'editor, quando si apre una pagina XAML. Nel riquadro di anteprima può essere visualizzato o nascosto premendo la **Preview** pulsante nell'angolo superiore destro di qualsiasi finestra del documento XAML:

[![Anteprima di controllo ListView in Visual Studio per Mac](xaml-previewer-images/xamlp-list-sml.png "Visualizzatore anteprima Forms in Visual Studio per Mac")](xaml-previewer-images/xamlp-list.png#lightbox "Visualizzatore anteprima Forms in Visual Studio per Mac")

-----

## <a name="xaml-preview-options"></a>Opzioni di anteprima XAML

Le opzioni nella parte superiore del riquadro di anteprima sono:

* **Telefono** : eseguire il rendering in una schermata di phone-dimensioni
* **Tablet** : eseguire il rendering in una schermata di Tablet PC-size (si noti sono presenti controlli zoom allo stato in basso a destra del riquadro)
* **Android** : Mostra la versione di Android della schermata
* **iOS** : Mostra la versione di iOS dello schermo
* Portait (icona): Usa l'orientamento verticale per l'anteprima
* Orizzontale (icona): Usa l'orientamento per l'anteprima orizzontale

## <a name="adding-design-time-data"></a>Aggiunta di dati Design-Time

Alcuni layout potrebbe essere difficile visualizzare senza dati associati a controlli dell'interfaccia utente. Per rendere più utile all'anteprima, assegnare alcuni dati statici per i controlli da impostare come hardcoded un contesto di associazione (sia nel code-behind o tramite XAML).

Fare riferimento a di James Montemagno [post di blog sull'aggiunta di dati design-time](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) per informazioni su come associare a un elemento ViewModel statiche in XAML.

## <a name="detecting-design-mode"></a>Rilevamento di modalità di progettazione

Il metodo statico [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) proprietà può essere esaminata per determinare se l'applicazione è in esecuzione nel Visualizzatore anteprima. In questo modo è possibile specificare il codice che verrà eseguita solo quando l'applicazione è in esecuzione nel Visualizzatore anteprima:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Controllare i problemi seguenti e il [forum di Xamarin](https://forums.xamarin.com/categories/xamarin-forms), se si verificano problemi.

### <a name="xaml-preview-isnt-showing"></a>Non è visualizzata l'anteprima XAML

Verificare quanto segue:

* Progetto deve essere compilato (compilata) prima di tentare di visualizzare in anteprima i file XAML.
* L'agente di progettazione deve essere configurato nel momento in che anteprima un file XAML, un indicatore di stato verrà visualizzati nel Visualizzatore anteprima, insieme ai messaggi di stato di avanzamento, fino a quando non si è pronto.
* Provare a chiudere e riaprire il file XAML.
* Verificare che il `App` classe ha un costruttore senza parametri.

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>XAML non valido: Il progetto Android deve compilare prima di poter creare l'anteprima

Il Visualizzatore anteprima XAML richiede che il progetto deve essere compilato prima del rendering di una pagina.
Se l'errore riportato di seguito viene visualizzata nella parte superiore del riquadro di anteprima, ricompilare l'applicazione e riprovare.

![Messaggio di errore: progetto deve essere compilato per primi](xaml-previewer-images/error-not-built-sml.png "messaggio di errore: ricompilare il progetto")
