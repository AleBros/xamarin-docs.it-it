---
title: "Visualizzatore anteprima XAML per Xamarin.Forms " Description: "in questo articolo viene illustrato come utilizzare il Visualizzatore anteprima XAML per visualizzare i Xamarin.Forms layout visualizzati durante la digitazione. Il Visualizzatore anteprima XAML è disponibile in Visual Studio 2019 e Visual Studio 2019 per Mac. "
zone_pivot_groups: piattaforma MS. prod: Novell MS. AssetID: 84769ff1-72fd-4C44-8251-dd6d5bf8c7b2 ms. Technology: Novell-Forms Author: maddyleger1 ms. Author: maleger ms. Date: 03/16/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xaml-previewer-for-xamarinforms"></a>Visualizzatore anteprima XAML perXamarin.Forms

_Visualizza i Xamarin.Forms layout visualizzati durante la digitazione_

## <a name="overview"></a>Panoramica

Il Visualizzatore anteprima XAML Mostra come verrà visualizzata la Xamarin.Forms pagina XAML in iOS e Android. Quando si apportano modifiche a XAML, verranno visualizzate in anteprima immediatamente insieme al codice. Il Visualizzatore anteprima XAML è disponibile in Visual Studio e Visual Studio per Mac.

## <a name="getting-started"></a>Introduzione

::: zone pivot="windows"

### <a name="visual-studio-2019"></a>Visual Studio 2019

È possibile aprire il Visualizzatore anteprima XAML facendo clic sulle frecce nel riquadro visualizzazione divisa. Se si desidera modificare il comportamento predefinito della visualizzazione divisa, utilizzare la finestra di dialogo **strumenti > opzioni > novell > visualizzatore Xamarin.Forms anteprime XAML** . In questa finestra di dialogo è possibile selezionare la visualizzazione del documento predefinita e l'orientamento della divisione.

[![Xamarin.FormsOpzioni anteprima in Visual Studio](xaml-previewer-images/xamlp-options-vs-sm.png "[! OP. NO-LOC (Novell. Forms)] Opzioni anteprima in Visual Studio")](xaml-previewer-images/xamlp-options-vs-lg.png#lightbox)

Quando si apre un file XAML, l'editor si aprirà a dimensione completa o accanto al Visualizzatore anteprima, in base alle impostazioni selezionate nella finestra di dialogo **strumenti > opzioni > novell > finestra di dialogo Xamarin.Forms Anteprima XAML** . Tuttavia, la suddivisione può essere modificata per ogni file nella finestra dell'editor.

#### <a name="xaml-preview-controls"></a>Controlli di anteprima XAML

Scegliere se si desidera visualizzare il codice, il Visualizzatore anteprima XAML o entrambi selezionando questi pulsanti nel riquadro visualizzazione divisa. Il pulsante centrale scambia il lato del Visualizzatore anteprima e del codice:

[![Xamarin.FormsControlli anteprima per passare dalla progettazione, dall'origine alla visualizzazione divisa in Visual Studio](xaml-previewer-images/xamlp-controls-splitview-vs-sm.png "[! OP. NO-LOC (Novell. Forms)] controlli visualizzatore anteprime per passare dalla visualizzazione progettazione, origine e suddivisione in Visual Studio")](xaml-previewer-images/xamlp-controls-splitview-vs-lg.png#lightbox)

È possibile modificare se la schermata è divisa verticalmente o orizzontalmente oppure comprimere completamente un riquadro:

[![Xamarin.FormsControlli di orientamento del riquadro anteprima in Visual Studio](xaml-previewer-images/xamlp-controls-orientation-vs-sm.png "[! OP. NO-LOC (Novell. Forms)] controlli dell'orientamento del riquadro anteprima in Visual Studio")](xaml-previewer-images/xamlp-controls-orientation-vs-lg.png#lightbox)

#### <a name="enable-or-disable-the-xaml-previewer"></a>Abilitare o disabilitare il Visualizzatore anteprima XAML

È possibile disattivare il Visualizzatore anteprima XAML in **strumenti > opzioni > novell > finestra di dialogo Visualizzatore Xamarin.Forms Anteprima XAML** selezionando **editor XML predefinito** come **editor XAML predefinito**. Questa operazione disattiva anche la struttura del documento, il pannello delle proprietà e la casella degli strumenti XAML. Per riattivare il Visualizzatore anteprima XAML e questi strumenti, modificare l' **editor XAML predefinito** in Visualizzatore ** Xamarin.Forms anteprime**.

::: zone-end
::: zone pivot="macos"

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

Il pulsante **Anteprima** viene visualizzato nell'editor quando si apre una pagina XAML. Mostrare o nascondere il Visualizzatore anteprime premendo i pulsanti **Anteprima** o **Dividi** nella parte inferiore sinistra di una finestra del documento XAML:

[![Xamarin.FormsVisualizzatore anteprime abilitato con il pulsante di anteprima o di divisione](xaml-previewer-images/xamlp-list-sml.png)](xaml-previewer-images/xamlp-list.png#lightbox)

> [!NOTE]
> Nelle versioni precedenti di Visual Studio per Mac, il pulsante **Anteprima** si trovava in alto a destra nella finestra.

#### <a name="enable-or-disable-the-xaml-previewer"></a>Abilitare o disabilitare il Visualizzatore anteprima XAML

È possibile disattivare il Visualizzatore anteprima XAML nella finestra di dialogo **Preferenze di Visual Studio > > editor di testo > XAML** selezionando **editor XML predefinito** come **editor XAML predefinito**. Questa operazione disattiva anche la struttura del documento, il pannello delle proprietà e la casella degli strumenti XAML. Per riattivare il Visualizzatore anteprima XAML e questi strumenti, modificare l' **editor XAML predefinito** in Visualizzatore ** Xamarin.Forms anteprime**.

::: zone-end

## <a name="xaml-previewer-options"></a>Opzioni anteprima XAML

Le opzioni nella parte superiore del riquadro di anteprima sono:

* **Android** : Mostra la versione di Android dello schermo
* **iOS** : Mostra la versione iOS dello schermo.*Nota: se si usa Visual Studio in Windows, è necessario [associarlo a un Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) per usare questa modalità.*
* **Dispositivo** : elenco a discesa di dispositivi Android o iOS, incluse la risoluzione e le dimensioni dello schermo
* **Verticale (icona)** : usa l'orientamento verticale per l'anteprima
* **Orizzontale (icona)** : usa l'orientamento orizzontale per l'anteprima

## <a name="detect-design-mode"></a>Rileva modalità progettazione

La [`DesignMode.IsDesignModeEnabled`](xref:Xamarin.Forms.DesignMode.IsDesignModeEnabled) proprietà statica indica se l'applicazione è in esecuzione nel Visualizzatore anteprima. Utilizzandola, è possibile specificare il codice che verrà eseguito solo quando l'applicazione è o non è in esecuzione nel Visualizzatore anteprima:

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

Questa proprietà è utile se si Inizializza una libreria nel costruttore della pagina che non viene eseguita in fase di progettazione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Controllare i problemi riportati di seguito e i [Forum di Novell](https://forums.xamarin.com/categories/xamarin-forms), se il Visualizzatore anteprima non funziona.

### <a name="xaml-previewer-isnt-showing-or-shows-an-error"></a>Il Visualizzatore anteprima XAML non Mostra o Mostra un errore

* L'avvio del Visualizzatore anteprime potrebbe richiedere del tempo. verrà visualizzato il "inizializzazione del rendering" fino a quando non è pronto.
* Provare a chiudere e riaprire il file XAML.
* Verificare che la `App` classe disponga di un costruttore senza parametri.
* Verificare la Xamarin.Forms versione: deve essere almeno Xamarin.Forms 3,6. È possibile eseguire l'aggiornamento alla Xamarin.Forms versione più recente tramite NuGet.
* Controllare l'installazione di JDK: l'anteprima di Android richiede almeno [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Provare a eseguire il wrapping di tutte le classi inizializzate nel codice C# della pagina dietro a `if (!DesignMode.IsDesignModeEnabled)` .

### <a name="custom-controls-arent-rendering"></a>Non è in esecuzione il rendering di controlli personalizzati

Provare a compilare il progetto. Il Visualizzatore anteprima Mostra la classe di base del controllo se non è in grado di eseguire il rendering del controllo o se il creatore del controllo ha escluso il rendering della fase di progettazione. Per altre informazioni, vedere [eseguire il rendering di controlli personalizzati nel Visualizzatore anteprime XAML](render-custom-controls.md).
