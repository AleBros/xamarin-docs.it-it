---
title: "funzionalità della piattaforma iOS in Xamarin.Forms " Description: "aggiunta di funzionalità specifiche per iOS alle Xamarin.Forms applicazioni".
ms. prod: Novell MS. AssetID: 634AB62E-68C8-454C-838B-F1CC4E4E21BC ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/05/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="ios-platform-features-in-xamarinforms"></a>funzionalità della piattaforma iOS inXamarin.Forms

Per lo sviluppo Xamarin.Forms di applicazioni per iOS è necessario Visual Studio. La [pagina piattaforme supportate](~/get-started/supported-platforms.md) contiene altre informazioni sui prerequisiti.

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati.

Per le Xamarin.Forms Visualizzazioni, le pagine e i layout in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti:

- Supporto di sfocatura per qualsiasi [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Per altre informazioni, vedere la pagina [relativa alla sfocatura di visualElement in iOS](visualelement-blur.md).
- Disabilitazione della modalità colore legacy su un supportato [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Per altre informazioni, vedere [modalità colore legacy di visualElement in iOS](legacy-color-mode.md).
- Abilitazione di un'ombreggiatura in un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Per ulteriori informazioni, vedere la pagina relativa all'eliminazione di oggetti [visivi in iOS](visualelement-drop-shadow.md).
- Consentire a un [`VisualElement`](xref:Xamarin.Forms.VisualElement) oggetto di diventare il primo risponditore per il tocco degli eventi. Per altre informazioni, vedere [primo risponditore di visualElement](visualelement-first-responder.md).

Per le visualizzazioni in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Impostazione del [`Cell`](xref:Xamarin.Forms.Cell) colore di sfondo. Per ulteriori informazioni, vedere la pagina [relativa al colore di sfondo della cella in iOS](cell-background-color.md).
- Controllo quando viene eseguita la selezione dell'elemento in un oggetto [`DatePicker`](xref:Xamarin.Forms.DatePicker) . Per altre informazioni, vedere [selezione di elementi DatePicker in iOS](datepicker-selection.md).
- Assicurandosi che il testo inserito venga inserito in un [`Entry`](xref:Xamarin.Forms.Entry) regolando le dimensioni del carattere. Per altre informazioni, vedere [entry font size in iOS](entry-font-size.md).
- Impostazione del colore del cursore in un oggetto [`Entry`](xref:Xamarin.Forms.Entry) . Per altre informazioni, vedere [entry Cursor color on iOS](entry-cursor-color.md).
- Controllare se [`ListView`](xref:Xamarin.Forms.ListView) le celle di intestazione fluttuano durante lo scorrimento. Per altre informazioni, vedere [stile intestazione gruppo ListView in iOS](listview-group-header-style.md).
- Controllo della disabilitazione delle animazioni di riga durante l' [`ListView`](xref:Xamarin.Forms.ListView) aggiornamento della raccolta di elementi. Per altre informazioni, vedere [animazioni di riga ListView in iOS](listview-row-animations.md).
- Impostazione dello stile del separatore su [`ListView`](xref:Xamarin.Forms.ListView) . Per altre informazioni, vedere [stile separatore ListView in iOS](listview-separator-style.md).
- Controllo quando viene eseguita la selezione dell'elemento in un oggetto [`Picker`](xref:Xamarin.Forms.Picker) . Per altre informazioni, vedere [selezione di elementi selezione in iOS](picker-selection.md).
- Controllo della presenza di uno [`SearchBar`](xref:Xamarin.Forms.SearchBar) sfondo. Per altre informazioni, vedere [stile di Searchbar in iOS](searchbar-style.md).
- Abilitazione della [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) proprietà da impostare toccando una posizione sulla [`Slider`](xref:Xamarin.Forms.Slider) barra, anziché trascinare il `Slider` cursore. Per altre informazioni, vedere [dispositivo di scorrimento tocco in iOS](slider-thumb.md).
- Controllo della transizione utilizzata durante l'apertura di un oggetto `SwipeView` . Per altre informazioni, vedere [modalità di transizione SwipeView swipe](swipeview-swipetransitionmode.md).
- Controllo quando viene eseguita la selezione dell'elemento in un oggetto [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Per altre informazioni, vedere [selezione di elementi TimePicker in iOS](timepicker-selection.md).

Per le pagine in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Controllo della presenza della pagina dei dettagli di un oggetto a cui è [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) applicata l'ombreggiatura quando viene rivelata la pagina master. Per ulteriori informazioni, vedere [MasterDetailPage Shadow](masterdetailpage-shadow.md).
- Nascondere il separatore della barra di navigazione in un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Per altre informazioni, vedere [separatore della barra NavigationPage in iOS](navigation-bar-separator.md).
- Controllo dell'eventuale traslucidità della barra di navigazione. Per altre informazioni, vedere [traslucidità della barra di spostamento in iOS](navigation-bar-translucent.md).
- Controllare se il colore del testo della barra di stato su un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene regolato in modo da corrispondere alla luminosità della barra di spostamento. Per altre informazioni, vedere la [modalità colore del testo della barra NavigationPage in iOS](status-bar-text-color.md).
- Controllare se il titolo della pagina viene visualizzato come titolo grande nella barra di spostamento della pagina. Per altre informazioni, vedere [titoli di pagine di grandi dimensioni in iOS](page-large-title.md).
- Impostazione della visibilità dell'indicatore Home in un oggetto [`Page`](xref:Xamarin.Forms.Page) . Per ulteriori informazioni, vedere [visibilità dell'indicatore Home in iOS](page-home-indicator.md).
- Impostazione della visibilità della barra di stato su un oggetto [`Page`](xref:Xamarin.Forms.Page) . Per ulteriori informazioni, vedere [visibilità della barra di stato della pagina in iOS](page-status-bar-visibility.md).
- Verificare che il contenuto della pagina sia posizionato in un'area della schermata sicura per tutti i dispositivi iOS. Per altre informazioni, vedere [Guida al layout dell'area sicura in iOS](page-safe-area-layout.md).
- Impostazione dello stile di presentazione delle pagine modali. Per altre informazioni, vedere [stile di presentazione della pagina modale](page-presentation-style.md).
- Impostazione della modalità di traslucidità della barra delle schede in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Per altre informazioni, vedere [TabbedPage translucidar in iOS](tabbedpage-translucent-tabbar.md).

Per i layout in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti Xamarin.Forms :

- Controllare se un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) gestisce un movimento tocco o lo passa al relativo contenuto. Per altre informazioni, vedere [ScrollView content touchs on iOS](scrollview-content-touches.md).

Per la Xamarin.Forms classe in iOS sono disponibili le funzionalità specifiche della piattaforma seguenti [`Application`](xref:Xamarin.Forms.Application) :

- Disabilitazione del ridimensionamento dell'accessibilità per le dimensioni del carattere denominato. Per altre informazioni, vedere [ridimensionamento dell'accessibilità per le dimensioni del carattere denominate in iOS](named-font-size-scaling.md).
- Abilitazione del layout e del rendering degli aggiornamenti del controllo da eseguire sul thread principale. Per ulteriori informazioni, vedere la pagina [relativa agli aggiornamenti principali del controllo thread in iOS](main-thread-updates-ui.md).
- Abilitazione di un oggetto [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) in una visualizzazione a scorrimento per acquisire e condividere il movimento della panoramica con la visualizzazione a scorrimento. Per altre informazioni, vedere [il riconoscimento dei movimenti di Pan simultanei in iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>formattazione specifica di iOS

Xamarin.Formsconsente di impostare stili e colori dell'interfaccia utente multipiattaforma, ma sono disponibili altre opzioni per l'impostazione del tema di iOS usando le API della piattaforma nel progetto iOS.

[Altre](formatting.md) informazioni sulla formattazione dell'interfaccia utente usando le API specifiche di iOS, ad esempio la configurazione di **info. plist** e l' `UIAppearance` API.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Altre funzionalità iOS

Utilizzando [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)e [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), è possibile incorporare un'ampia gamma di funzionalità native nelle Xamarin.Forms applicazioni per iOS.
