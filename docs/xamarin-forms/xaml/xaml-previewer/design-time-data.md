---
title: "utilizzare i dati della fase di progettazione con il Visualizzatore anteprima XAML" Descrizione: "in questo articolo viene illustrato come utilizzare i dati della fase di progettazione per visualizzare layout intensivi dei dati nel Visualizzatore anteprima XAML senza eseguire l'app."
ms. prod: Novell MS. AssetID: 0F608019-5951-4BE6-80E0-9EEE1733D642 ms. Technology: Novell-Forms Author: maddyleger1 ms. Author: maleger ms. Date: 03/27/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="use-design-time-data-with-the-xaml-previewer"></a>Usare i dati della fase di progettazione con il Visualizzatore anteprima XAML

_Alcuni layout sono difficili da visualizzare senza dati. Usare questi suggerimenti per sfruttare al meglio l'anteprima delle pagine con dati intensivi nel Visualizzatore anteprime XAML._

## <a name="design-time-data-basics"></a>Nozioni fondamentali sui dati in fase di progettazione

I dati della fase di progettazione sono dati falsi impostati per semplificare la visualizzazione dei controlli nel Visualizzatore anteprime XAML. Per iniziare, aggiungere le righe di codice seguenti all'intestazione della pagina XAML:

```xaml
xmlns:d="http://xamarin.com/schemas/2014/forms/design"
xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
mc:Ignorable="d"
```

Dopo aver aggiunto gli spazi dei nomi, è possibile posizionarli `d:` davanti a qualsiasi attributo o controllo per visualizzarli nel Visualizzatore anteprime XAML. Gli elementi con `d:` non vengono visualizzati in fase di esecuzione.

Ad esempio, è possibile aggiungere testo a un'etichetta che in genere contiene dati associati.

```xaml
<Label Text="{Binding Name}" d:Text="Name!" />
```

[![Progettare dati in fase di progettazione con testo in un'etichetta](xaml-previewer-images/designtimedata-label-sm.png "Dati della fase di progettazione con un'etichetta di testo")](xaml-previewer-images/designtimedata-label-lg.png#lightbox)

In questo esempio, senza `d:Text` , il Visualizzatore anteprima XAML non visualizzerà nulla per l'etichetta. Viene invece visualizzato "nome!" dove l'etichetta avrà dati reali in fase di esecuzione.

È possibile utilizzare `d:` con qualsiasi attributo per un Xamarin.Forms controllo, ad esempio i colori, le dimensioni del carattere e la spaziatura. È anche possibile aggiungerlo al controllo stesso:

```xaml
<d:Button Text="Design Time Button" />
```

[![Dati della fase di progettazione con un controllo Button](xaml-previewer-images/designtimedata-controls-sm.png "Dati della fase di progettazione con un controllo Button")](xaml-previewer-images/designtimedata-controls-lg.png#lightbox)

In questo esempio, il pulsante viene visualizzato solo in fase di progettazione. Utilizzare questo metodo per inserire un segnaposto in per un [controllo personalizzato non supportato dal Visualizzatore anteprima XAML](render-custom-controls.md).

## <a name="preview-images-at-design-time"></a>Anteprima immagini in fase di progettazione

È possibile impostare un'origine della fase di progettazione per le immagini associate alla pagina o caricate in modo dinamico. Nel progetto Android aggiungere l'immagine che si vuole visualizzare nel Visualizzatore anteprima XAML per le **risorse >** cartella che è possibile creare. Nel progetto iOS aggiungere l'immagine alla cartella **Resources** . È quindi possibile visualizzare l'immagine nel Visualizzatore anteprime XAML in fase di progettazione:

```xaml
<Image Source={Binding ProfilePicture} d:Source="DesignTimePicture.jpg" />
```

[![Dati della fase di progettazione con immagini](xaml-previewer-images/designtimedata-image-sm.png "Dati della fase di progettazione con iamges")](xaml-previewer-images/designtimedata-image-lg.png#lightbox)

## <a name="design-time-data-for-listviews"></a>Dati della fase di progettazione per ListView

I ListView sono un modo comune per visualizzare i dati in un'app per dispositivi mobili. Tuttavia, sono difficili da visualizzare senza dati reali. Per utilizzare i dati relativi alla fase di progettazione, è necessario creare una matrice della fase di progettazione da utilizzare come ItemsSource. Il Visualizzatore anteprima XAML Visualizza gli elementi presenti nella matrice in fase di progettazione in ListView.

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type x:String}">
                <x:String>Item One</x:String>
                <x:String>Item Two</x:String>
                <x:String>Item Three</x:String>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding ItemName}"
                          d:Text="{Binding .}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

[![Dati della fase di progettazione con ListView](xaml-previewer-images/designtimedata-itemssource-sm.png "Dati della fase di progettazione con ListView")](xaml-previewer-images/designtimedata-itemssource-lg.png#lightbox)

In questo esempio viene mostrato un ListView di tre TextCells nel Visualizzatore anteprima XAML. È possibile passare `x:String` a un modello di dati esistente nel progetto.

È inoltre possibile creare una matrice di oggetti dati. Ad esempio, le proprietà pubbliche di un `Monkey` oggetto dati possono essere costruite come dati della fase di progettazione:

```csharp
namespace Monkeys.Models
{
    public class Monkey
    {
        public string Name { get; set; }
        public string Location { get; set; }
    }
}
```

Per usare la classe in XAML, è necessario importare lo spazio dei nomi nel nodo radice:

```xaml
xmlns:models="clr-namespace:Monkeys.Models"
```

```xaml
<StackLayout>
    <ListView ItemsSource="{Binding Items}">
        <d:ListView.ItemsSource>
            <x:Array Type="{x:Type models:Monkey}">
                <models:Monkey Name="Baboon" Location="Africa and Asia"/>
                <models:Monkey Name="Capuchin Monkey" Location="Central and South America"/>
                <models:Monkey Name="Blue Monkey" Location="Central and East Africa"/>
            </x:Array>
        </d:ListView.ItemsSource>
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="models:Monkey">
                <TextCell Text="{Binding Name}"
                          Detail="{Binding Location}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackLayout>
```

Il vantaggio è che è possibile eseguire l'associazione al modello effettivo che si prevede di usare.

## <a name="alternative-hardcode-a-static-viewmodel"></a>Alternativa: impostare come hardcoded un elemento ViewModel statico

Se non si desidera aggiungere i dati della fase di progettazione ai singoli controlli, è possibile configurare un archivio dati fittizio da associare alla pagina. Vedere il post di Blog di James Montemagno su [sull'aggiunta di dati in fase di progettazione](https://montemagno.com/xamarin-forms-design-time-data-tips-best-practices/) per vedere come eseguire l'associazione a un ViewModel statico in XAML.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="requirements"></a>Requisiti

I dati della fase di progettazione richiedono almeno la versione Xamarin.Forms 3,6.

### <a name="intellisense-shows-squiggly-lines-under-my-design-time-data"></a>IntelliSense mostra le linee ondulate nei dati della fase di progettazione

Si tratta di un problema noto che verrà risolto in una versione futura di Visual Studio. Il progetto viene comunque compilato senza errori.

### <a name="the-xaml-previewer-stopped-working"></a>Il Visualizzatore anteprima XAML ha smesso di funzionare

Provare a chiudere e riaprire il file XAML e a pulire e ricompilare il progetto.
