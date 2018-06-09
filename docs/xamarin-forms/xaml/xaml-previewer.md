---
title: Visualizzatore di XAML per xamarin. Forms
description: In questo articolo viene illustrato come utilizzare il Visualizzatore di XAML per visualizzare i layout di xamarin. Forms sottoposto a rendering durante la digitazione. Il Visualizzatore di XAML è disponibile in Visual Studio 2017 sia Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 84769ff1-72fd-4c44-8251-dd6d5bf8c7b2
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/31/2018
ms.openlocfilehash: 25c8e1a34f8be5ab2f8491e75fa5aac470d55bc8
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245859"
---
# <a name="xaml-previewer-for-xamarinforms"></a>Visualizzatore di XAML per xamarin. Forms

_Vedere i layout di xamarin. Forms sottoposto a rendering durante la digitazione._

## <a name="requirements"></a>Requisiti

I progetti richiedono il pacchetto NuGet di xamarin. Forms più recente per l'anteprima di XAML a funzionare. Visualizzazione in anteprima le app Android richiede [JDK 1.8 x64](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

Altre informazioni, vedere è il [note sulla versione](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#Xamarin_Forms_Previewer).

## <a name="getting-started"></a>Introduzione

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Utilizzare il **Vista > altre finestre > Anteprima di xamarin. Forms** menu in Visual Studio per aprire la finestra di anteprima. Utilizzare il **finestra > gruppo di schede verticali** menu per posizionarla side-by-side.

[![Anteprima di controllo ListView in Visual Studio](xaml-previewer-images/xamlp-list-vs-sml.png "anteprima di form in Visual Studio")](xaml-previewer-images/xamlp-list-vs.png#lightbox "anteprima di form in Visual Studio")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Il **anteprima** pulsante può essere visualizzato nell'editor facendo clic su un file XAML e selezionando **Apri con > Anteprima form**. Nel riquadro di anteprima può quindi essere mostrato o nascosto premendo il **anteprima** pulsante nell'angolo superiore destro della finestra di un documento XAML:

[![Anteprima di controllo ListView in Visual Studio per Mac](xaml-previewer-images/xamlp-list-sml.png "anteprima di form in Visual Studio per Mac")](xaml-previewer-images/xamlp-list.png#lightbox "anteprima di form in Visual Studio per Mac")

-----

## <a name="xaml-preview-options"></a>Opzioni di anteprima XAML

Le opzioni nella parte superiore del riquadro di anteprima sono:

* **Telefono** : eseguire il rendering in una schermata di dimensioni di telefono
* **Tablet** : eseguire il rendering in una schermata tablet-dimensione (si noti sono presenti controlli zoom nella parte inferiore del riquadro)
* **Android** : Mostra la versione Android dello schermo
* **iOS** : Mostra la versione di iOS dello schermo
* Portait (icona): utilizza l'orientamento verticale per l'anteprima
* Orizzontale (icona) – utilizza orizzontale per l'anteprima

## <a name="adding-design-time-data"></a>Aggiunta di dati in fase di progettazione

Alcuni layout possono essere difficili da visualizzare senza dati associati a controlli dell'interfaccia utente. Per visualizzare l'anteprima più utile, assegnare alcuni dati statici ai controlli, hardcoded un contesto di associazione (sia nel code-behind o tramite XAML).

Fare riferimento a di James Montemagno [post di blog sull'aggiunta di dati in fase di progettazione](http://motzcod.es/post/143702671962/xamarinforms-xaml-previewer-design-time-data) per informazioni su come associare un ViewModel statico in XAML.

## <a name="detecting-design-mode"></a>Individuazione modalità progettazione

Il metodo statico [ `DesignMode.IsDesignModeEnabled` ](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) proprietà può essere esaminata per determinare se l'applicazione è in esecuzione nell'anteprima. Ciò consente di specificare il codice che verrà eseguito solo quando l'applicazione è in esecuzione nell'anteprima:

```csharp
if (DesignMode.IsDesignModeEnabled)
{
  // Previewer only code  
}
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

Controllare i problemi indicati di seguito e [Xamarin forum](https://forums.xamarin.com/categories/xamarin-forms), se si verificano problemi.

### <a name="xaml-preview-isnt-showing"></a>Non è visualizzata l'anteprima di XAML

Verificare quanto segue:

* Progetto deve essere compilato (compilata) prima di tentare di visualizzare in anteprima i file XAML.
* L'agente di progettazione deve essere configurazione la prima volta che si visualizza l'anteprima di un file XAML, un indicatore di stato verrà visualizzati nell'anteprima, insieme ai messaggi di stato di avanzamento, fino a quando non si è pronto.
* Provare a chiudere e riaprire il file XAML.

### <a name="invalid-xaml-the-android-project-needs-to-built-before-preview-can-be-created"></a>XAML non valido: Il progetto Android deve compilare prima anteprima può essere creata

Il Visualizzatore di XAML richiede che il progetto deve essere compilato prima del rendering di una pagina.
Se l'errore riportato di seguito viene visualizzata nella parte superiore del riquadro di anteprima, ricompilare l'applicazione e riprovare.

![Messaggio di errore: progetto da compilare prima](xaml-previewer-images/error-not-built-sml.png "messaggio di errore: ricompilare il progetto")
