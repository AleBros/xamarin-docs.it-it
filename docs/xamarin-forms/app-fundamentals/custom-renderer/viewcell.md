---
title: Personalizzazione di un ViewCell
description: "Un ViewCell xamarin. Forms è una cella che può essere aggiunti a un controllo ListView o TableView, che contiene una visualizzazione definita dallo sviluppatore. In questo articolo viene illustrato come creare un renderer personalizzato per un ViewCell che è ospitato all'interno di un controllo ListView di xamarin. Forms. In tal modo i calcoli di layout di xamarin. Forms il chiamato più volte durante lo scorrimento di ListView."
ms.topic: article
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 2c65bce7ae468ef07c6d898e3f532aa95580f2ba
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="customizing-a-viewcell"></a>Personalizzazione di un ViewCell

_Un ViewCell xamarin. Forms è una cella che può essere aggiunti a un controllo ListView o TableView, che contiene una visualizzazione definita dallo sviluppatore. In questo articolo viene illustrato come creare un renderer personalizzato per un ViewCell che è ospitato all'interno di un controllo ListView di xamarin. Forms. In tal modo i calcoli di layout di xamarin. Forms il chiamato più volte durante lo scorrimento di ListView._

Ogni cella di xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) viene eseguito il rendering da un'applicazione di xamarin. Forms, in iOS il `ViewCellRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `UITableViewCell` controllo. Nella piattaforma Android, il `ViewCellRenderer` un'istanza nativa `View` controllo. In Windows Phone e di Windows della piattaforma UWP (Universal), il `ViewCellRenderer` un'istanza nativa `DataTemplate`. Per ulteriori informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) e i corrispondenti controlli nativi che l'implementano:

![](viewcell-images/viewcell-classes.png "Relazione tra il controllo ViewCell e i controlli nativi di implementazione")

Il processo di rendering può essere sfruttato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Cell) una cella personalizzata xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Cell) della cella personalizzata di xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per la cella in ogni piattaforma.

Ogni elemento verrà ora essere descritti in dettaglio, per implementare un `NativeCell` renderer che si avvale di un layout specifico della piattaforma per ogni cella ospitato all'interno di un xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controllo. In tal modo i calcoli di layout di xamarin. Forms essere chiamato più volte durante `ListView` lo scorrimento.

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>Creazione della cella personalizzata

È possibile creare un controllo di cella personalizzato creando sottoclassi di [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) classe, come illustrato nell'esempio di codice seguente:

```csharp
public class NativeCell : ViewCell
{
  public static readonly BindableProperty NameProperty =
    BindableProperty.Create ("Name", typeof(string), typeof(NativeCell), "");

  public string Name {
    get { return (string)GetValue (NameProperty); }
    set { SetValue (NameProperty, value); }
  }

  public static readonly BindableProperty CategoryProperty =
    BindableProperty.Create ("Category", typeof(string), typeof(NativeCell), "");

  public string Category {
    get { return (string)GetValue (CategoryProperty); }
    set { SetValue (CategoryProperty, value); }
  }

  public static readonly BindableProperty ImageFilenameProperty =
    BindableProperty.Create ("ImageFilename", typeof(string), typeof(NativeCell), "");

  public string ImageFilename {
    get { return (string)GetValue (ImageFilenameProperty); }
    set { SetValue (ImageFilenameProperty, value); }
  }
}
```
La `NativeCell` classe viene creata nel progetto di libreria (PCL) di classi portabile e definisce l'API per la cella personalizzata. Espone la cella personalizzata `Name`, `Category`, e `ImageFilename` proprietà che possono essere visualizzate tramite l'associazione dati. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>Utilizzo di cella personalizzato

Il `NativeCell` cella personalizzato può fare riferimento in Xaml nel progetto libreria di classi Portabile dichiarare uno spazio dei nomi per la posizione e utilizzando il prefisso dello spazio dei nomi dell'elemento di cella personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come la `NativeCell` cella personalizzati può essere utilizzato da una pagina XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
        <StackLayout>
            <Label Text="Xamarin.Forms native cell" HorizontalTextAlignment="Center" />
            <ListView x:Name="listView" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <local:NativeCell Name="{Binding Name}" Category="{Binding Category}" ImageFilename="{Binding ImageFilename}" />
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Il `local` prefisso dello spazio dei nomi denominato nulla. Tuttavia, il `clr-namespace` e `assembly` i valori devono corrispondere i dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene utilizzato per fare riferimento alla cella personalizzata.

Nell'esempio di codice riportato di seguito viene illustrato come la `NativeCell` cella personalizzati può essere utilizzato da una pagina in c#:

```csharp
public class NativeCellPageCS : ContentPage
{
    ListView listView;

    public NativeCellPageCS()
    {
        listView = new ListView(ListViewCachingStrategy.RecycleElement)
        {
            ItemsSource = DataSource.GetList(),
            ItemTemplate = new DataTemplate(() =>
            {
                var nativeCell = new NativeCell();
                nativeCell.SetBinding(NativeCell.NameProperty, "Name");
                nativeCell.SetBinding(NativeCell.CategoryProperty, "Category");
                nativeCell.SetBinding(NativeCell.ImageFilenameProperty, "ImageFilename");

                return nativeCell;
            })
        };

        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
                Padding = new Thickness(0, 20, 0, 0);
                break;
            case Device.Android:
            case Device.UWP:
                Padding = new Thickness(0);
                break;
        }

        Content = new StackLayout
        {
            Children = {
                new Label { Text = "Xamarin.Forms native cell", HorizontalTextAlignment = TextAlignment.Center },
                listView
            }
        };
        listView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Un xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controllo viene utilizzato per visualizzare un elenco di dati, che viene compilati tramite il [ `ItemSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemsSource/) proprietà. Il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) strategia di memorizzazione nella cache tenta di ridurre al minimo il `ListView` footprint di memoria e di esecuzione velocità riciclando celle di elenco. Per ulteriori informazioni, vedere [strategia di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

Ogni riga nell'elenco contiene tre elementi di dati, un nome, una categoria e un nome di file di immagine. Il layout di ogni riga nell'elenco viene definito per il `DataTemplate` a cui fa riferimento tramite il [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%601.ItemTemplate/) proprietà associabile. Il `DataTemplate` definisce che ogni riga di dati nell'elenco sarà un `NativeCell` che consente di visualizzare il relativo `Name`, `Category`, e `ImageFilename` proprietà tramite associazione dati. Per ulteriori informazioni sul `ListView` di controllo, vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per personalizzare il layout specifici della piattaforma per ogni cella.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione di Renderer personalizzato a seconda della piattaforma

Il processo di creazione della classe renderer personalizzato è come segue:

1. Creare una sottoclasse di `ViewCellRenderer` classe che esegue il rendering di cella personalizzato.
1. Override del metodo specifico della piattaforma che esegue il rendering di cella personalizzato e scrivere una logica per personalizzarlo.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering della cella personalizzata di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> **Nota**: per la maggior parte degli elementi di xamarin. Forms, è facoltativo fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, verrà utilizzato il renderer predefinito per la classe di base del controllo. Un renderer personalizzato è tuttavia necessarie in ogni progetto della piattaforma per il rendering di un [ViewCell](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) elemento.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](viewcell-images/solution-structure.png "Responsabilità di progetto personalizzato NativeCell Renderer")

Il `NativeCell` viene eseguito il rendering di cella personalizzati dalle classi renderer specifico della piattaforma, che derivano dalla `ViewCellRenderer` classe per ogni piattaforma. Di conseguenza, ogni `NativeCell` cella personalizzato viene eseguito il rendering con layout specifici della piattaforma, come illustrato nelle schermate seguenti:

![](viewcell-images/screenshots.png "NativeCell in ciascuna piattaforma")

La `ViewCellRenderer` classe espone i metodi specifici della piattaforma per il rendering di cella personalizzato. Questo è il `GetCell` metodo nella piattaforma iOS, il `GetCellCore` metodo nella piattaforma Android e il `GetTemplate` metodo sulla piattaforma Windows Phone.

Ogni classe renderer personalizzato è decorato con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo di cella xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo si applica all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma iOS:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeiOSCellRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        NativeiOSCell cell;

        public override UITableViewCell GetCell(Cell item, UITableViewCell reusableCell, UITableView tv)
        {
            var nativeCell = (NativeCell)item;

            cell = reusableCell as NativeiOSCell;
            if (cell == null)
                cell = new NativeiOSCell(item.GetType().FullName, nativeCell);
            else
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;
            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

Il `GetCell` metodo viene chiamato per compilare ogni cella deve essere visualizzato. Ogni cella è un `NativeiOSCell` istanza, che definisce il layout della cella e i relativi dati. L'operazione del `GetCell` è dipende dal metodo di [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) strategia di memorizzazione nella cache:

- Quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) strategia di memorizzazione nella cache è [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/), `GetCell` metodo verrà richiamato per ogni cella. Oggetto `NativeiOSCell` verrà creata per ogni istanza `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. Quando l'utente scorre il `ListView`, `NativeiOSCell` istanze verrà nuovamente usate. Per ulteriori informazioni sulla cella iOS nuovamente utilizzo, vedere [cella riutilizzare](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Questo codice renderer personalizzato verrà eseguite alcune celle riutilizzare anche quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è impostata per mantenere le celle.

  I dati visualizzati da ciascun `NativeiOSCell` istanza, se appena creato o riutilizzato, verrà aggiornata con i dati da ogni `NativeCell` istanza il `UpdateCell` metodo.

  > [!NOTE]
  > Il `OnNativeCellPropertyChanged` (metodo) non potranno mai essere richiamato quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) strategia di memorizzazione nella cache è impostata per mantenere le celle.

- Quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) strategia di memorizzazione nella cache è [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/), `GetCell` metodo verrà richiamato per ogni cella che viene inizialmente visualizzato sullo schermo. Oggetto `NativeiOSCell` verrà creata per ogni istanza `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. I dati visualizzati da ciascun `NativeiOSCell` verrà aggiornata con i dati da un'istanza di `NativeCell` istanza il `UpdateCell` metodo. Tuttavia, il `GetCell` metodo non verrà richiamato quando l'utente scorre il `ListView`. Al contrario, il `NativeiOSCell` istanze verrà nuovamente usate. `PropertyChanged` vengono generati eventi nel `NativeCell` istanza quando i dati cambiano e `OnNativeCellPropertyChanged` gestore dell'evento per aggiornare i dati in ogni riutilizzate `NativeiOSCell` istanza.

Nell'esempio di codice riportato di seguito viene illustrato il `OnNativeCellPropertyChanged` metodo che ha richiamato quando un `PropertyChanged` evento:

```csharp
namespace CustomRenderer.iOS
{
    public class NativeiOSCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingLabel.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingLabel.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.CellImageView.Image = cell.GetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Questo metodo aggiorna i dati visualizzati da riutilizzate `NativeiOSCell` istanze. Viene eseguito un controllo per la proprietà che è stato modificato, come il metodo può essere chiamato più volte.

La `NativeiOSCell` classe definisce il layout per ogni cella e viene illustrato nell'esempio di codice seguente:

```csharp
internal class NativeiOSCell : UITableViewCell, INativeElementView
{
  public UILabel HeadingLabel { get; set; }
  public UILabel SubheadingLabel { get; set; }
  public UIImageView CellImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeiOSCell(string cellId, NativeCell cell) : base(UITableViewCellStyle.Default, cellId)
  {
    NativeCell = cell;

    SelectionStyle = UITableViewCellSelectionStyle.Gray;
    ContentView.BackgroundColor = UIColor.FromRGB(255, 255, 224);
    CellImageView = new UIImageView();

    HeadingLabel = new UILabel()
    {
      Font = UIFont.FromName("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB(127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    SubheadingLabel = new UILabel()
    {
      Font = UIFont.FromName("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB(38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add(HeadingLabel);
    ContentView.Add(SubheadingLabel);
    ContentView.Add(CellImageView);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingLabel.Text = cell.Name;
    SubheadingLabel.Text = cell.Category;
    CellImageView.Image = GetImage(cell.ImageFilename);
  }

  public UIImage GetImage(string filename)
  {
    return (!string.IsNullOrWhiteSpace(filename)) ? UIImage.FromFile("Images/" + filename + ".jpg") : null;
  }

  public override void LayoutSubviews()
  {
    base.LayoutSubviews();

    HeadingLabel.Frame = new CGRect(5, 4, ContentView.Bounds.Width - 63, 25);
    SubheadingLabel.Frame = new CGRect(100, 18, 100, 20);
    CellImageView.Frame = new CGRect(ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Questa classe definisce i controlli utilizzati per eseguire il rendering, il contenuto della cella e il layout. La classe implementa il [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) interfaccia, che è obbligatorio quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utilizza il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) strategia di memorizzazione nella cache. Questa interfaccia consente di specificare che la classe deve implementare il [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) proprietà, che deve restituire i dati delle celle personalizzata per le celle riciclate.

Il `NativeiOSCell` costruttore inizializza l'aspetto del `HeadingLabel`, `SubheadingLabel`, e `CellImageView` proprietà. Queste proprietà vengono utilizzate per visualizzare i dati archiviati nel `NativeCell` istanza, con la `UpdateCell` metodo chiamato per impostare il valore di ogni proprietà. Inoltre, quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utilizza il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) la memorizzazione nella cache i dati visualizzati dalla strategia di `HeadingLabel`, `SubheadingLabel`, e `CellImageView` le proprietà possono essere aggiornando il `OnNativeCellPropertyChanged` metodo nel renderer personalizzato.

Layout della cella viene eseguita dal `LayoutSubviews` esegue l'override, che imposta le coordinate di `HeadingLabel`, `SubheadingLabel`, e `CellImageView` all'interno della cella.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeAndroidCellRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        NativeAndroidCell cell;

        protected override Android.Views.View GetCellCore(Cell item, Android.Views.View convertView, ViewGroup parent, Context context)
        {
            var nativeCell = (NativeCell)item;
            Console.WriteLine("\t\t" + nativeCell.Name);

            cell = convertView as NativeAndroidCell;
            if (cell == null)
            {
                cell = new NativeAndroidCell(context, nativeCell);
            }
            else
            {
                cell.NativeCell.PropertyChanged -= OnNativeCellPropertyChanged;
            }

            nativeCell.PropertyChanged += OnNativeCellPropertyChanged;

            cell.UpdateCell(nativeCell);
            return cell;
        }
        ...
    }
}
```

Il `GetCellCore` metodo viene chiamato per compilare ogni cella deve essere visualizzato. Ogni cella è un `NativeAndroidCell` istanza, che definisce il layout della cella e i relativi dati. L'operazione del `GetCellCore` è dipende dal metodo di [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) strategia di memorizzazione nella cache:

- Quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) strategia di memorizzazione nella cache è [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/), `GetCellCore` metodo verrà richiamato per ogni cella. Oggetto `NativeAndroidCell` verrà creata per ogni `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. Quando l'utente scorre il `ListView`, `NativeAndroidCell` istanze verrà nuovamente usate. Per ulteriori informazioni sulla cella Android riutilizzo, vedere [riga visualizzazione usare nuovamente](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Si noti che questo codice renderer personalizzato verrà eseguite alcune celle riutilizzare anche quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è impostata per mantenere le celle.

  I dati visualizzati da ciascun `NativeAndroidCell` istanza, se appena creato o riutilizzato, verrà aggiornata con i dati da ogni `NativeCell` istanza il `UpdateCell` metodo.

  > [!NOTE]
  > Si noti che, sebbene il `OnNativeCellPropertyChanged` metodo verrà richiamato quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è impostata per mantenere le celle, non verrà aggiornata la `NativeAndroidCell` i valori delle proprietà.

- Quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) strategia di memorizzazione nella cache è [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/), `GetCellCore` metodo verrà richiamato per ogni cella che viene inizialmente visualizzato sullo schermo. Oggetto `NativeAndroidCell` verrà creata per ogni istanza `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. I dati visualizzati da ciascun `NativeAndroidCell` verrà aggiornata con i dati da un'istanza di `NativeCell` istanza il `UpdateCell` metodo. Tuttavia, il `GetCellCore` metodo non verrà richiamato quando l'utente scorre il `ListView`. Al contrario, il `NativeAndroidCell` istanze verrà nuovamente usate.  `PropertyChanged` vengono generati eventi nel `NativeCell` istanza quando i dati cambiano e `OnNativeCellPropertyChanged` gestore dell'evento per aggiornare i dati in ogni riutilizzate `NativeAndroidCell` istanza.

Nell'esempio di codice riportato di seguito viene illustrato il `OnNativeCellPropertyChanged` metodo che ha richiamato quando un `PropertyChanged` evento:

```csharp
namespace CustomRenderer.Droid
{
    public class NativeAndroidCellRenderer : ViewCellRenderer
    {
        ...

        void OnNativeCellPropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var nativeCell = (NativeCell)sender;
            if (e.PropertyName == NativeCell.NameProperty.PropertyName)
            {
                cell.HeadingTextView.Text = nativeCell.Name;
            }
            else if (e.PropertyName == NativeCell.CategoryProperty.PropertyName)
            {
                cell.SubheadingTextView.Text = nativeCell.Category;
            }
            else if (e.PropertyName == NativeCell.ImageFilenameProperty.PropertyName)
            {
                cell.SetImage(nativeCell.ImageFilename);
            }
        }
    }
}
```

Questo metodo aggiorna i dati visualizzati da riutilizzate `NativeAndroidCell` istanze. Viene eseguito un controllo per la proprietà che è stato modificato, come il metodo può essere chiamato più volte.

La `NativeAndroidCell` classe definisce il layout per ogni cella e viene illustrato nell'esempio di codice seguente:

```csharp
internal class NativeAndroidCell : LinearLayout, INativeElementView
{
  public TextView HeadingTextView { get; set; }
  public TextView SubheadingTextView { get; set; }
  public ImageView ImageView { get; set; }

  public NativeCell NativeCell { get; private set; }
  public Element Element => NativeCell;

  public NativeAndroidCell(Context context, NativeCell cell) : base(context)
  {
    NativeCell = cell;

    var view = (context as Activity).LayoutInflater.Inflate(Resource.Layout.NativeAndroidCell, null);
    HeadingTextView = view.FindViewById<TextView>(Resource.Id.HeadingText);
    SubheadingTextView = view.FindViewById<TextView>(Resource.Id.SubheadingText);
    ImageView = view.FindViewById<ImageView>(Resource.Id.Image);

    AddView(view);
  }

  public void UpdateCell(NativeCell cell)
  {
    HeadingTextView.Text = cell.Name;
    SubheadingTextView.Text = cell.Category;

    // Dispose of the old image
    if (ImageView.Drawable != null)
    {
      using (var image = ImageView.Drawable as BitmapDrawable)
      {
        if (image != null)
        {
          if (image.Bitmap != null)
          {
            image.Bitmap.Dispose();
          }
        }
      }
    }

    SetImage(cell.ImageFilename);
  }

  public void SetImage(string filename)
  {
    if (!string.IsNullOrWhiteSpace(filename))
    {
      // Display new image
      Context.Resources.GetBitmapAsync(filename).ContinueWith((t) =>
      {
        var bitmap = t.Result;
        if (bitmap != null)
        {
          ImageView.SetImageBitmap(bitmap);
          bitmap.Dispose();
        }
      }, TaskScheduler.FromCurrentSynchronizationContext());
    }
    else
    {
      // Clear the image
      ImageView.SetImageBitmap(null);
    }
  }
}
```

Questa classe definisce i controlli utilizzati per eseguire il rendering, il contenuto della cella e il layout. La classe implementa il [ `INativeElementView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.INativeElementView/) interfaccia, che è obbligatorio quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utilizza il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) strategia di memorizzazione nella cache. Questa interfaccia consente di specificare che la classe deve implementare il [ `Element` ](https://developer.xamarin.com/api/property/Xamarin.Forms.INativeElementView.Element/) proprietà, che deve restituire i dati delle celle personalizzata per le celle riciclate.

Il `NativeAndroidCell` costruttore incrementa il `NativeAndroidCell` layout e inizializza il `HeadingTextView`, `SubheadingTextView`, e `ImageView` proprietà ai controlli nel layout ingrandito. Queste proprietà vengono utilizzate per visualizzare i dati archiviati nel `NativeCell` istanza, con la `UpdateCell` metodo chiamato per impostare il valore di ogni proprietà. Inoltre, quando il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) utilizza il [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) la memorizzazione nella cache i dati visualizzati dalla strategia di `HeadingTextView`, `SubheadingTextView`, e `ImageView` le proprietà possono essere aggiornando il `OnNativeCellPropertyChanged` metodo nel renderer personalizzato.

Esempio di codice seguente illustra la definizione di layout per il `NativeAndroidCell.axml` file di layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:background="@drawable/CustomSelector">
    <LinearLayout
        android:id="@+id/Text"
        android:orientation="vertical"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingLeft="10dip">
        <TextView
            android:id="@+id/HeadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/SubheadingText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textSize="14dip"
            android:textColor="#FF267F00"
            android:paddingLeft="100dip" />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout>
```

Questo layout specifica che due `TextView` controlli e un `ImageView` controllo consentono di visualizzare il contenuto della cella. I due `TextView` i controlli sono orientati in verticale all'interno di un `LinearLayout` controllo con tutti i controlli sia indipendente all'interno di un `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>Creazione di Renderer personalizzato in Windows Phone e UWP

Esempio di codice seguente viene illustrato il renderer personalizzato per Windows Phone e UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeWinPhoneCellRenderer))]
namespace CustomRenderer.WinPhone81
{
    public class NativeWinPhoneCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

Il `GetTemplate` metodo viene chiamato per restituire la cella da sottoporre a rendering per ogni riga di dati nell'elenco. Crea un `DataTemplate` per ogni `NativeCell` istanza che verrà visualizzato sullo schermo, con la `DataTemplate` che definisce l'aspetto e il contenuto della cella.

Il `DataTemplate` vengono archiviati nel dizionario risorse a livello di applicazione e viene illustrato nell'esempio di codice seguente:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="LightYellow">
        <Grid.Resources>
            <local:ConcatImageExtensionConverter x:Name="ConcatImageExtensionConverter" />
        </Grid.Resources>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.40*" />
            <ColumnDefinition Width="0.40*"/>
            <ColumnDefinition Width="0.20*" />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.ColumnSpan="2" Foreground="#7F3300" FontStyle="Italic" FontSize="22" VerticalAlignment="Top" Text="{Binding Name}" />
        <TextBlock Grid.RowSpan="2" Grid.Column="1" Foreground="#267F00" FontWeight="Bold" FontSize="12" VerticalAlignment="Bottom" Text="{Binding Category}" />
        <Image Grid.RowSpan="2" Grid.Column="2" HorizontalAlignment="Left" VerticalAlignment="Center" Source="{Binding ImageFilename, Converter={StaticResource ConcatImageExtensionConverter}}" Width="50" Height="50" />
        <Line Grid.Row="1" Grid.ColumnSpan="3" X1="0" X2="1" Margin="30,20,0,0" StrokeThickness="1" Stroke="LightGray" Stretch="Fill" VerticalAlignment="Bottom" />
    </Grid>
</DataTemplate>
```

Il `DataTemplate` specifica i controlli utilizzati per visualizzare il contenuto della cella e il layout e l'aspetto. Due `TextBlock` controlli e un `Image` controllo consentono di visualizzare il contenuto della cella tramite l'associazione dati. Inoltre, un'istanza del `ConcatImageExtensionConverter` utilizzato per concatenare il `.jpg` estensione per il nome di ogni file di immagine. In questo modo il `Image` controllo è possibile caricare ed eseguire il rendering dell'immagine quando è `Source` proprietà è impostata.

## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato come creare un renderer personalizzato per un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) che è ospitato all'interno di un xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controllo. In tal modo i calcoli di layout di xamarin. Forms essere chiamato più volte durante `ListView` lo scorrimento.


## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
