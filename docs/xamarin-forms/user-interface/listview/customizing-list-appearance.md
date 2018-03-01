---
title: Elenco aspetto
description: "Personalizzare i controlli ListView utilizzando le intestazioni, piè di pagina, gruppi e le celle di altezza variabile."
ms.topic: article
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: c828a48342fc1b387dab2884dbb4aa5d82faebdd
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="list-appearance"></a>Elenco aspetto

`ListView` sono disponibili opzioni per controllare la presentazione dell'elenco globale, oltre a sottostante `ViewCell`s. Le opzioni includono:

- [**Raggruppamento** ](#Grouping) &ndash; raggruppare gli elementi di ListView per semplificare l'esplorazione e una migliore organizzazione.
- [**Intestazioni e piè di pagina** ](#Headers_and_Footers) &ndash; visualizzare le informazioni all'inizio e alla fine della vista che scorre con gli altri elementi.
- [**Separatori di riga** ](#Row_Separators) &ndash; mostrare o nascondere le righe di separazione tra gli elementi.
- [**Le righe di altezza variabile** ](#Row_Heights) &ndash; per impostazione predefinita, tutte le righe hanno la stessa altezza, ma può essere modificato per consentire le righe con altezza diversi da visualizzare.

<a name="Grouping" />

## <a name="grouping"></a>Raggruppamento
Spesso, grandi set di dati può diventare difficile da gestire quando presentati in un elenco di scorrimento continuo. L'abilitazione di raggruppamento può migliorare l'esperienza utente in questi casi per organizzare meglio il contenuto e l'attivazione di controlli specifici della piattaforma che consentono di semplificare lo spostamento di dati.

Quando il raggruppamento viene attivato per un `ListView`, viene aggiunta una riga di intestazione per ogni gruppo.

Per abilitare il raggruppamento:

- Creare un elenco di elenchi (un elenco di gruppi, ogni gruppo in un elenco di elementi).
- Impostare il `ListView`del `ItemsSource` a tale elenco.
- Impostare `IsGroupingEnabled` su true.
- Impostare [ `GroupDisplayBinding` ](http://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupDisplayBinding/) da associare alla proprietà dei gruppi che viene utilizzata come il titolo del gruppo.
- [Facoltativo] Impostare [ `GroupShortNameBinding` ](http://developer.xamarin.com/api/property/Xamarin.Forms.ListView.GroupShortNameBinding/) da associare alla proprietà dei gruppi che viene utilizzata come il nome breve per il gruppo. Il nome breve viene utilizzato per gli elenchi di salto (colonna lato rigt in iOS, Windows Phone riquadro griglia).

Iniziare creando una classe per i gruppi:

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

Nel codice precedente, `All` è riportato l'elenco che verrà assegnato al nostro controllo ListView come origine di associazione. `Title` e `ShortName` sono le proprietà che verranno utilizzate per le intestazioni di gruppo.

In questa fase, `All` è un elenco vuoto. Aggiungere un costruttore statico, in modo che l'elenco verrà popolato all'avvio del programma:

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alfa", "A"){
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
        }
        All = Groups; //set the publicly accessible list
}
```

Nel codice precedente è inoltre possibile chiamare `Add` sugli elementi di `groups`, che sono istanze di tipo `PageTypeGroup`. Ciò è possibile perché `PageTypeGroup` eredita da `List<PageModel>`. Questo è un esempio dell'elenco di elenchi motivo indicato in precedenza.

Di seguito è riportato il codice XAML per la visualizzazione dell'elenco di gruppo:

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

L'opzione produce quanto segue:

![](customizing-list-appearance-images/grouping-depth.png "Esempio di raggruppamento di ListView")

Si noti che è necessario:

- Impostare `GroupShortNameBinding` per il `ShortName` proprietà definita nella classe di gruppo
- Impostare `GroupDisplayBinding` per il `Title` proprietà definita nella classe di gruppo
- Impostare `IsGroupingEnabled` su true
- Modificare il `ListView`del `ItemsSource` all'elenco di gruppi

### <a name="customizing-grouping"></a>Personalizzazione di raggruppamento
Ora che abbiamo visto come implementare base raggruppamento in ListView, di seguito viene illustrato come personalizzare la visualizzazione delle intestazioni di gruppo.

Simile alla modalità `ListView` ha un `ItemTemplate` per definire la modalità di visualizzazione delle righe, `ListView` ha un `GroupHeaderTemplate`. Questo è un esempio di ListView precedente, con un modello di intestazione di un gruppo personalizzato:

![](customizing-list-appearance-images/grouping-depth.png "ListView con GroupHeaderTemplate personalizzato")


Di seguito è illustrato come eseguire tale progettazione in XAML:

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
            <!-- End Group Header Customization
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

<a name="Headers_and_Footers" />

## <a name="headers-and-footers"></a>Intestazioni e piè di pagina
È possibile che un controllo ListView presentare un'intestazione e piè di pagina che lo scorrimento con gli elementi dell'elenco. L'intestazione e piè di pagina può essere stringhe di testo o un layout più complesso. Si noti che questo è separato dal [sezione gruppi](#Grouping).

È possibile impostare il `Header` e/o `Footer` in una stringa semplice oppure è possibile impostarli su un layout più complesso.
Sono inoltre disponibili `HeaderTemplate` e `FooterTemplate` proprietà che consentono di creare un layout più complessi per l'intestazione e piè di pagina che supportano il data binding.

Per creare un semplice intestazione/piè di pagina, impostare solo le proprietà di intestazione o piè di pagina per il testo che si desidera visualizzare. In codice:

```csharp
ListView HeaderList = new ListView() {
    Header = "Header",
    Footer = "Footer"
    };
```

In XAML:

```xaml
<ListView  x:Name="HeaderList"  Header="Header" Footer="Footer"></ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView con intestazione e piè di pagina")

Per creare un'intestazione personalizzata e un piè di pagina, definire le visualizzazioni di intestazione e piè di pagina:

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

![](customizing-list-appearance-images/header-custom.png "ListView con intestazione personalizzata e un piè di pagina")

<a name="Row_Separators" />

## <a name="row-separators"></a>Separatori di riga
Viene visualizzata una linea di separazione tra `ListView` elementi per impostazione predefinita in iOS e Android. Windows Phone non supporta le righe di separatore, per tale piattaforme linee guida UX. Se si preferisce nascondere le righe di separazione in iOS e Android, impostare il `SeparatorVisibility` proprietà in ListView. Le opzioni per `SeparatorVisibility` sono:

* **Predefinito** -Mostra una linea di separazione in iOS e Android.
* **Nessuna** -nasconde il separatore in tutte le piattaforme.

Visibilità predefinita:

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView con separatori di riga predefinita")

None:

C#:

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView senza separatori di riga")

È inoltre possibile impostare il colore della linea di separazione tramite il `SeparatorColor` proprietà:

C#:

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView con separatori di riga di colore verde")

**Nota**: impostazione di queste proprietà in Android dopo il caricamento di `ListView` comporta una riduzione delle prestazioni di grandi dimensioni.

<a name="Row_Heights" />

## <a name="row-heights"></a>Altezza delle righe
Per impostazione predefinita, tutte le righe in un controllo ListView hanno la stessa altezza. ListView con due proprietà che può essere usata per modificare questo comportamento:

- `HasUnevenRows` &ndash; `true`/`false` valore, le righe hanno altezze se impostato su `true`. Il valore predefinito è `false`.
- `RowHeight` &ndash; Imposta l'altezza di ogni riga quando `HasUnevenRows` è `false`.

È possibile impostare l'altezza di tutte le righe mediante l'impostazione di `RowHeight` proprietà il `ListView`.

### <a name="custom-fixed-row-height"></a>Altezza di riga predefinito personalizzato

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView con altezza fissa")


### <a name="uneven-rows"></a>Righe non uniforme

Se si desidera singole righe di altezza diversa, è possibile impostare il `HasUnevenRows` proprietà `true`.
Si noti che non può essere impostato manualmente una volta altezza delle righe `HasUnevenRows` è stata impostata su `true`, poiché l'altezza verrà calcolata automaticamente da xamarin. Forms.


C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView con righe dispari")

### <a name="runtime-resizing-of-rows"></a>Runtime di ridimensionamento di righe

Singoli `ListView` righe possono essere ridimensionate a livello di codice in fase di esecuzione, a condizione che il `HasUnevenRows` è impostata su `true`. Il [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/) metodo aggiorna dimensioni di una cella, anche quando non è attualmente visibile, come illustrato nell'esempio di codice seguente:

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

Il `OnImageTapped` gestore eventi viene eseguito in risposta a un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) vengano utilizzate in una cella e aumenta le dimensioni del `Image` visualizzato nella cella in modo che sia visualizzato facilmente.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView con il ridimensionamento di righe di Runtime")

Si noti che esiste una possibilità sicura di riduzione delle prestazioni se questa funzionalità è eccessivo.



## <a name="related-links"></a>Collegamenti correlati

- [Raggruppamento (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [Visualizzazione di rendering personalizzata (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [Ridimensionamento di righe dinamiche (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/DynamicUnevenListCells/)
- [note sulla versione 1.4](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [note sulla versione 1.3](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
