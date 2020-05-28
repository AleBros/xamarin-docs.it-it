---
title: Description:' questo articolo illustra il supporto della piattaforma Windows disponibile in Xamarin.Forms .'
ms. prod: ms. AssetID: ms. Technology: autore: ms. Author: ms. Date: No-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="windows-platform-features"></a>Funzionalità della piattaforma Windows

Per lo sviluppo Xamarin.Forms di applicazioni per piattaforme Windows è necessario Visual Studio. La [pagina piattaforme supportate](~/get-started/supported-platforms.md) contiene altre informazioni sui prerequisiti.

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati.

Le seguenti funzionalità specifiche della piattaforma sono disponibili per Xamarin.Forms Visualizzazioni, pagine e layout nella piattaforma UWP (Universal Windows Platform) (UWP):

- Impostazione di una chiave di accesso per un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Per ulteriori informazioni, vedere la pagina [relativa alle chiavi di accesso di visualElement in Windows](visualelement-access-keys.md).
- Disabilitazione della modalità colore legacy su un supportato [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Per altre informazioni, vedere [modalità colore legacy di visualElement in Windows](legacy-color-mode.md).

Per le visualizzazioni in UWP sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Rilevamento dell'ordine di lettura dal contenuto di testo nelle [`Entry`](xref:Xamarin.Forms.Entry) istanze di, [`Editor`](xref:Xamarin.Forms.Editor) e [`Label`](xref:Xamarin.Forms.Label) . Per ulteriori informazioni, vedere l' [ordine di lettura di InputView in Windows](inputview-reading-order.md).
- Abilitazione del supporto del movimento tap in un [`ListView`](xref:Xamarin.Forms.ListView) . Per ulteriori informazioni, vedere [ListView SelectionMode in Windows](listview-selectionmode.md).
- Abilitazione della direzione di pull di un oggetto `RefreshView` da modificare. Per ulteriori informazioni, vedere [RefreshView pull direction in Windows](refreshview-pulldirection.md).
- Consentire a un oggetto [`SearchBar`](xref:Xamarin.Forms.SearchBar) di interagire con il motore di controllo ortografico. Per ulteriori informazioni, vedere [SearchBar controllo ortografico in Windows](searchbar-spell-check.md).
- Abilitazione [`WebView`](xref:Xamarin.Forms.WebView) di un oggetto per visualizzare gli avvisi JavaScript in una finestra di dialogo di messaggio UWP. Per altre informazioni, vedere [WebView javascript Alerts on Windows](webview-javascript-alert.md).

Per le pagine in UWP sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Compressione della [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) barra di spostamento. Per ulteriori informazioni, vedere [barra di spostamento MasterDetailPage in Windows](masterdetailpage-navigation-bar.md).
- Impostazione delle opzioni di posizionamento della barra degli strumenti. Per ulteriori informazioni, vedere [posizionamento della barra degli strumenti delle pagine in Windows](page-toolbar-placement.md).
- Abilitazione delle icone di pagina da visualizzare su una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra degli strumenti. Per altre informazioni, vedere [Icone TabbedPage in Windows](tabbedpage-icons.md).

Per la Xamarin.Forms classe in UWP sono disponibili le funzionalità specifiche della piattaforma seguenti [`Application`](xref:Xamarin.Forms.Application) :

- Specificando la directory nel progetto da cui verranno caricati gli asset di immagine. Per ulteriori informazioni, vedere [default image directory in Windows](default-image-directory.md).

## <a name="platform-support"></a>Piattaforme supportate

I Xamarin.Forms modelli disponibili in Visual Studio contengono un progetto piattaforma UWP (Universal Windows Platform) (UWP).

> [!NOTE]
> Xamarin.Forms1. x e 2. x supportano _Windows Phone 8 Silverlight_, _Windows Phone 8,1_e _Windows 8.1_ lo sviluppo di applicazioni. Tuttavia, questi tipi di progetto sono stati deprecati.

## <a name="getting-started"></a>Introduzione

Per iniziare, passare a **File > nuovo progetto di >** in Visual Studio e scegliere uno dei modelli di **app vuota > più piattaforme ( Xamarin.Forms )** .

Xamarin.FormsPer le soluzioni precedenti, o quelle create in MacOS, non saranno disponibili tutti i progetti Windows elencati sopra (ma è necessario aggiungerli manualmente). Se la piattaforma Windows di destinazione non è già presente nella soluzione, vedere le [istruzioni di installazione](installation/index.md) per aggiungere il tipo o i tipi di progetto Windows desiderati.

## <a name="samples"></a>Esempi

[Tutti gli esempi per il](https://github.com/xamarin/xamarin-forms-book-preview-2) libro di Charles Petzold [*creazione di app Xamarin.Forms per dispositivi mobili con*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) includono piattaforma UWP (Universal Windows Platform) (per i progetti Windows 10).

L' [app demo "Scott hanselr"](https://github.com/jamesmontemagno/Hanselman.Forms) è disponibile separatamente e include anche Apple Watch e Android Wear Projects (usando Novell. iOS e Novell. Android rispettivamente, Xamarin.Forms non viene eseguito su tali piattaforme).

## <a name="related-links"></a>Collegamenti correlati

- [Imposta progetti Windows](~/xamarin-forms/platform/windows/installation/index.md)
