---
title: "la posizione e il colore della barra degli strumenti di TabbedPage in Android" Description: "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma Android che imposta la posizione e il colore della barra degli strumenti in un TabbedPage ".
ms. prod: Novell MS. AssetID: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Posizionamento e colore della barra degli strumenti TabbedPage in Android

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> Le specifiche della piattaforma che impostano il colore della barra degli strumenti in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) sono ora obsolete e sono state sostituite dalle [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) proprietà e. Per altre informazioni, vedere [creare un TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage).

Queste specifiche della piattaforma vengono usate per impostare la posizione e il colore della barra degli strumenti in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Vengono utilizzati in XAML impostando la [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) proprietà associata su un valore dell' [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumerazione e e le [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) proprietà associate a un oggetto [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

In alternativa, possono essere usati da C# usando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

Il `TabbedPage.On<Android>` metodo specifica che queste specifiche della piattaforma vengono eseguite solo su Android. [ `TabbedPage.SetToolbarPlacement` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Il metodo PlatformConfiguration. AndroidSpecific. ToolbarPlacement), nello [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi, viene usato per impostare la posizione della barra degli strumenti su un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , con l' [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumerazione che fornisce i valori seguenti:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default): indica che la barra degli strumenti viene posizionata nella posizione predefinita della pagina. Si tratta della parte superiore della pagina sui telefoni e della parte inferiore della pagina in altri idiomi del dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top): indica che la barra degli strumenti è posizionata nella parte superiore della pagina.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom): indica che la barra degli strumenti è posizionata nella parte inferiore della pagina.

Inoltre, [ `TabbedPage.SetBarItemColor` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Color)) e [ `TabbedPage.SetBarSelectedItemColor` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . )) I metodi vengono usati per impostare rispettivamente il colore degli elementi della barra degli strumenti e gli elementi selezionati della barra degli strumenti.

> [!NOTE]
> [ `GetToolbarPlacement` ] (Xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})), [ `GetBarItemColor` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) e [ `GetBarSelectedItemColor` ] (xrif: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) i metodi possono essere utilizzati per recuperare la posizione e il colore della [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra degli strumenti.

Il risultato è che la posizione della barra degli strumenti, il colore degli elementi della barra degli strumenti e il colore dell'elemento della barra degli strumenti selezionato possono essere impostati su [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
