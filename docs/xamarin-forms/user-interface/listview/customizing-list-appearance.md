---
title: Aspetto ListView
description: Questo articolo illustra come personalizzare i ListView nelle Xamarin.Forms applicazioni usando le intestazioni, i piè di pagina, i gruppi e le celle a altezza variabile.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c7fdecdb0ce209c88dbe9e6f4e6e6588ec4fd3fd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139919"
---
# <a name="listview-appearance"></a>Aspetto ListView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) Consente di personalizzare la presentazione dell'elenco, oltre alle [`ViewCell`](xref:Xamarin.Forms.ViewCell) istanze per ogni riga nell'elenco.

## <a name="grouping"></a>Raggruppamento

Un set di dati di grandi dimensioni può diventare difficoltoso quando viene presentato in un elenco a scorrimento continuo. L'abilitazione del raggruppamento può migliorare l'esperienza utente in questi casi, organizzando meglio il contenuto e attivando controlli specifici della piattaforma che semplificano lo spostamento dei dati.

Quando il raggruppamento viene attivato per un oggetto `ListView` , viene aggiunta una riga di intestazione per ogni gruppo.

Per abilitare il raggruppamento:

- Creare un elenco di elenchi (un elenco di gruppi, ogni gruppo che è un elenco di elementi).
- Impostare l' `ListView` oggetto `ItemsSource` su tale elenco.
- Impostare `IsGroupingEnabled` su true.
- Impostare [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) per eseguire l'associazione alla proprietà dei gruppi utilizzati come titolo del gruppo.
- Opzionale Impostare [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) per eseguire l'associazione alla proprietà dei gruppi utilizzati come nome breve per il gruppo. Il nome breve viene usato per le Jump List (colonna sul lato destro in iOS).

Per iniziare, creare una classe per i gruppi:

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

Nel codice precedente, `All` è l'elenco che verrà assegnato al ListView come origine dell'associazione. `Title`e `ShortName` sono le proprietà che verranno usate per le intestazioni di gruppo.

In questa fase, `All` è un elenco vuoto. Aggiungere un costruttore statico in modo che l'elenco venga popolato all'avvio del programma:

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alpha", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        };
        All = Groups; //set the publicly accessible list
}
```

Nel codice precedente, è anche possibile chiamare `Add` sugli elementi di `Groups` , che sono istanze di tipo `PageTypeGroup` . Questo metodo è possibile perché `PageTypeGroup` eredita da `List<PageModel>` .

Di seguito è riportato il codice XAML per la visualizzazione dell'elenco raggruppato:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
                   GroupDisplayBinding="{Binding Title}"
                   GroupShortNameBinding="{Binding ShortName}"
                   IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Questo codice XAML esegue le azioni seguenti:

- Impostare sulla `GroupShortNameBinding` `ShortName` proprietà definita nella classe gruppo
- Impostare sulla `GroupDisplayBinding` `Title` proprietà definita nella classe gruppo
- Imposta `IsGroupingEnabled` su true
- È stato modificato l'oggetto nell' `ListView` `ItemsSource` elenco raggruppato

La schermata seguente mostra l'interfaccia utente risultante:

![](customizing-list-appearance-images/grouping-depth.png "ListView Grouping Example")

### <a name="customizing-grouping"></a>Personalizzazione del raggruppamento

Se il raggruppamento è stato abilitato nell'elenco, è possibile personalizzare anche l'intestazione di gruppo.

In modo analogo a `ListView` per la definizione della modalità di visualizzazione delle `ItemTemplate` righe, `ListView` dispone di un oggetto `GroupHeaderTemplate` .

Di seguito è riportato un esempio di personalizzazione dell'intestazione di gruppo in XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
                  GroupDisplayBinding="{Binding Title}"
                  GroupShortNameBinding="{Binding ShortName}"
                  IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding ShortName}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization -->
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

## <a name="headers-and-footers"></a>Intestazioni e piè di pagina

È possibile che un controllo ListView presenti un'intestazione e un piè di pagina che scorrono con gli elementi dell'elenco. L'intestazione e il piè di pagina possono essere stringhe di testo o un layout più complesso. Questo comportamento è separato dai [gruppi di sezioni](#grouping).

È possibile impostare `Header` e/o `Footer` su un `string` valore oppure è possibile impostarli su un layout più complesso. Sono inoltre disponibili `HeaderTemplate` `FooterTemplate` proprietà e che consentono di creare layout più complessi per l'intestazione e il piè di pagina che supportano data binding.

Per creare un'intestazione o un piè di pagina di base, è sufficiente impostare le proprietà dell'intestazione o del piè di pagina sul testo che si desidera visualizzare. Nel codice:

```csharp
ListView HeaderList = new ListView()
{
    Header = "Header",
    Footer = "Footer"
};
```

In XAML:

```xaml
<ListView x:Name="HeaderList" 
          Header="Header"
          Footer="Footer">
    ...
</ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView with Header and Footer")

Per creare un'intestazione e un piè di pagina personalizzati, definire le visualizzazioni dell'intestazione e del piè di pagina:

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
               TextColor="Olive"
               BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
               TextColor="Gray"
               BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "ListView with Customized Header and Footer")

## <a name="scrollbar-visibility"></a>Visibilità della barra di scorrimento

La [`ListView`](xref:Xamarin.Forms.ListView) classe dispone `HorizontalScrollBarVisibility` di `VerticalScrollBarVisibility` proprietà e, che ottengono o impostano un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valore che rappresenta quando la barra di scorrimento orizzontale o verticale è visibile. Entrambe le proprietà possono essere impostate sui valori seguenti:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)indica il comportamento predefinito della barra di scorrimento per la piattaforma e è il valore predefinito per `HorizontalScrollBarVisibility` le `VerticalScrollBarVisibility` proprietà e.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)indica che le barre di scorrimento saranno visibili, anche quando il contenuto si adatterà alla visualizzazione.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)indica che le barre di scorrimento non saranno visibili, anche se il contenuto non rientra nella visualizzazione.

## <a name="row-separators"></a>Separatori di riga

Per impostazione predefinita, le linee dei separatori vengono visualizzate tra `ListView` gli elementi in iOS e Android. Se si preferisce nascondere le linee dei separatori in iOS e Android, impostare la `SeparatorVisibility` proprietà nel controllo ListView. Le opzioni per `SeparatorVisibility` sono:

- **Impostazione predefinita** : Mostra una linea di separazione in iOS e Android.
- **Nessuno** : nasconde il separatore su tutte le piattaforme.

Visibilità predefinita:

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView with Default Row Separators")

None:

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView without Row Separators")

È anche possibile impostare il colore della linea di separazione tramite la `SeparatorColor` proprietà:

C#:

```csharp
SeparatorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView with Green Row Separators")

> [!NOTE]
> Se si imposta una di queste proprietà in Android dopo il caricamento di, si verifica `ListView` un notevole calo delle prestazioni.

## <a name="row-height"></a>Altezza riga

Per impostazione predefinita, tutte le righe di un controllo ListView hanno la stessa altezza. ListView dispone di due proprietà che possono essere utilizzate per modificare tale comportamento:

- `HasUnevenRows`&ndash; `true`/`false` valore, le righe hanno altezze variabili se impostate su `true` . Il valore predefinito è `false`.
- `RowHeight`&ndash;imposta l'altezza di ogni riga quando `HasUnevenRows` è `false` .

È possibile impostare l'altezza di tutte le righe impostando la `RowHeight` proprietà nell'oggetto `ListView` .

### <a name="custom-fixed-row-height"></a>Altezza riga fissa personalizzata

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView with Fixed Row Height")

### <a name="uneven-rows"></a>Righe non uniformi

Se si desidera che le singole righe abbiano altezze diverse, è possibile impostare la `HasUnevenRows` proprietà su `true` . Non è necessario impostare manualmente le altezze delle righe quando `HasUnevenRows` è stato impostato su `true` , perché le altezze verranno calcolate automaticamente da Xamarin.Forms .

C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView with Uneven Rows")

### <a name="resize-rows-at-runtime"></a>Ridimensionare le righe in fase di esecuzione

`ListView`Le singole righe possono essere ridimensionate a livello di codice in fase di esecuzione, purché la `HasUnevenRows` proprietà sia impostata su `true` . Il [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) metodo aggiorna le dimensioni di una cella, anche quando non è attualmente visibile, come illustrato nell'esempio di codice seguente:

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

Il `OnImageTapped` gestore eventi viene eseguito in risposta a un oggetto [`Image`](xref:Xamarin.Forms.Image) in una cella da toccare e aumenta le dimensioni dell'oggetto `Image` visualizzato nella cella in modo che sia facilmente visualizzabile.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView with Runtime Row Resizing")

> [!WARNING]
> Un utilizzo eccessivo del ridimensionamento delle righe di runtime può causare un calo delle prestazioni.

## <a name="related-links"></a>Collegamenti correlati

- [Raggruppamento (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Visualizzazione renderer personalizzata (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Ridimensionamento dinamico delle righe (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [Note sulla versione di 1,4](https://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [Note sulla versione di 1,3](https://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
