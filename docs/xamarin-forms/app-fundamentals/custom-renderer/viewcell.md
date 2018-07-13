---
title: Personalizzazione di un elemento ViewCell
description: Un elemento ViewCell xamarin. Forms è una cella che è possibile aggiungere a un ListView o TableView, che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come creare un renderer personalizzato per un elemento ViewCell ospitato all'interno di un controllo ListView di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: b1ebe2694ad5fa996b8b679cfb31a203588de05c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998999"
---
# <a name="customizing-a-viewcell"></a>Personalizzazione di un elemento ViewCell

_Un elemento ViewCell xamarin. Forms è una cella che è possibile aggiungere a un ListView o TableView, che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come creare un renderer personalizzato per un elemento ViewCell ospitato all'interno di un controllo ListView di xamarin. Forms. Questo arresta i calcoli di layout di xamarin. Forms venga chiamato più volte durante lo scorrimento di ListView._

Tutte le celle di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) viene eseguito il rendering da un'applicazione xamarin. Forms, in iOS il `ViewCellRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `UITableViewCell` controllo. La piattaforma Android, il `ViewCellRenderer` un'istanza nativa `View` controllo. In Universal Windows Platform (UWP), il `ViewCellRenderer` un'istanza nativa `DataTemplate`. Per altre informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) e i corrispondenti controlli nativi che l'implementano:

![](viewcell-images/viewcell-classes.png "Relazione tra i controlli nativi di implementazione e il controllo elemento ViewCell")

Il processo di rendering possibile avvantaggiarsi di implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Cell) una cella personalizzata a xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Cell) la cella personalizzata da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per la cella in ogni piattaforma.

Ogni elemento a questo punto discuteremo a sua volta, per implementare una `NativeCell` renderer che si avvale di un layout specifico della piattaforma per ogni cella ospitato all'interno di un xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) controllo. Questo arresta i calcoli di layout di xamarin. Forms venga chiamato più volte durante `ListView` lo scorrimento.

<a name="Creating_the_Custom_Cell" />

## <a name="creating-the-custom-cell"></a>Creazione della cella personalizzata

È possibile creare un controllo di cella personalizzato creando sottoclassi il [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) classe, come illustrato nell'esempio di codice seguente:

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
Il `NativeCell` classe viene creata nel progetto della libreria .NET Standard e definisce l'API per la cella personalizzata. La cella personalizzata espone `Name`, `Category`, e `ImageFilename` proprietà che possono essere visualizzate tramite associazione dati. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Consuming_the_Custom_Cell" />

## <a name="consuming-the-custom-cell"></a>Utilizzo la cella personalizzata

Il `NativeCell` cella personalizzata può fare riferimento nel codice Xaml nel progetto della libreria .NET Standard dichiara uno spazio dei nomi per la relativa posizione e usando il prefisso dello spazio dei nomi dell'elemento di cella personalizzato. Nell'esempio di codice riportato di seguito viene illustrato come il `NativeCell` cella personalizzato può essere usato da una pagina XAML:

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

Il `local` prefisso dello spazio dei nomi può essere denominata alcuna operazione. Tuttavia, il `clr-namespace` e `assembly` valori devono corrispondere i dettagli del controllo personalizzato. Una volta lo spazio dei nomi è dichiarato, il prefisso viene utilizzato per fare riferimento alla cella personalizzata.

Nell'esempio di codice riportato di seguito viene illustrato come il `NativeCell` cella personalizzato può essere usato da una pagina di c#:

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

Un xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) controllo viene usato per visualizzare un elenco di dati, che viene compilati tramite il [ `ItemSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà. Il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di cache tenta di ridurre al minimo il `ListView` footprint di memoria e l'esecuzione più veloci per riciclare le celle di elenco. Per altre informazioni, vedere [strategia di memorizzazione nella cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

Ogni riga nell'elenco contiene tre elementi di dati, un nome, una categoria e un nome di file di immagine. Il layout di ogni riga nell'elenco è definito dal `DataTemplate` che viene fatto riferimento tramite il [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà associabile. Il `DataTemplate` definisce che ogni riga di dati nell'elenco sarà un `NativeCell` che consente di visualizzare relativo `Name`, `Category`, e `ImageFilename` proprietà tramite associazione dati. Per altre informazioni sul `ListView` controllano, vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

Un renderer personalizzato può ora essere aggiunti a ogni progetto di applicazione per personalizzare il layout specifici della piattaforma per ogni cella.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creare il Renderer personalizzato a seconda della piattaforma

Il processo per la creazione della classe renderer personalizzati è come segue:

1. Creare una sottoclasse del `ViewCellRenderer` classe che esegue il rendering la cella personalizzata.
1. L'override del metodo specifico della piattaforma che esegue il rendering di cella personalizzato e scrivere la logica per personalizzarla.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering della cella personalizzata di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> Per la maggior parte degli elementi di xamarin. Forms, è facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, quindi verrà utilizzato il renderer predefinito per la classe di base del controllo. Renderer personalizzati sono tuttavia necessarie in ogni progetto della piattaforma durante il rendering di un [elemento ViewCell](xref:Xamarin.Forms.ViewCell) elemento.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](viewcell-images/solution-structure.png "Responsabilità di progetto Renderer personalizzati NativeCell")

Il `NativeCell` viene eseguito il rendering di cella personalizzato dalle classi renderer specifica della piattaforma, che derivano da tutti i `ViewCellRenderer` classe per ogni piattaforma. Di conseguenza ogni `NativeCell` cella personalizzato da visualizzare con layout specifici della piattaforma, come illustrato negli screenshot seguenti:

![](viewcell-images/screenshots.png "NativeCell in ogni piattaforma")

Il `ViewCellRenderer` classe espone i metodi specifici della piattaforma per il rendering la cella personalizzata. Questo è il `GetCell` metodo nella piattaforma iOS, il `GetCellCore` metodo nella piattaforma Android e il `GetTemplate` nella piattaforma UWP (metodo).

Ogni classe renderer personalizzato è decorata con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo della cella di xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

Il `GetCell` metodo viene chiamato per compilare ogni cella da visualizzare. Ogni cella è una `NativeiOSCell` istanza, che definisce il layout della cella e dei relativi dati. L'operazione dei `GetCell` metodo dipende il [ `ListView` ](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache:

- Quando la [ `ListView` ](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache viene [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), il `GetCell` metodo verrà richiamato per ogni cella. Oggetto `NativeiOSCell` viene creata per ogni istanza `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. Mentre l'utente scorre attraverso il `ListView`, `NativeiOSCell` istanze verrà nuovamente usate. Per altre informazioni su iOS cella nuovamente usare, vedere [cella riutilizzare](~/ios/user-interface/controls/tables/populating-a-table-with-data.md).

  > [!NOTE]
  > Questo codice di renderer personalizzato verrà eseguite alcune celle usare di nuovo, anche se il [ `ListView` ](xref:Xamarin.Forms.ListView) è impostata per conservare le celle.

  I dati visualizzati da ognuno `NativeiOSCell` istanza, appena creata o usata nuovamente, verrà aggiornata con i dati da ognuno `NativeCell` dell'istanza dal `UpdateCell` (metodo).

  > [!NOTE]
  > Il `OnNativeCellPropertyChanged` metodo non potranno mai essere richiamato quando il [ `ListView` ](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache è impostata per conservare le celle.

- Quando la [ `ListView` ](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache viene [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), il `GetCell` metodo verrà richiamato per ogni cella che viene inizialmente visualizzato sullo schermo. Oggetto `NativeiOSCell` viene creata per ogni istanza `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. I dati visualizzati da ognuno `NativeiOSCell` verrà aggiornata con i dati da un'istanza di `NativeCell` istanza dal `UpdateCell` (metodo). Tuttavia, il `GetCell` metodo non verranno chiamato quando l'utente scorre il `ListView`. Al contrario, il `NativeiOSCell` istanze verrà nuovamente usate. `PropertyChanged` verranno generati eventi sul `NativeCell` modifica dei dati, di istanze e il `OnNativeCellPropertyChanged` gestore dell'evento verrà aggiornati i dati in ogni riusati `NativeiOSCell` istanza.

Nell'esempio di codice riportato di seguito viene illustrato il `OnNativeCellPropertyChanged` metodo che ha richiamato quando un `PropertyChanged` viene generato l'evento:

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

Questo metodo aggiorna i dati visualizzati riusati `NativeiOSCell` istanze. Viene eseguito un controllo per la proprietà che è stato modificato, come il metodo può essere chiamato più volte.

Il `NativeiOSCell` classe definisce il layout per ogni cella e viene illustrato nell'esempio di codice seguente:

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

Questa classe definisce i controlli usati per eseguire il rendering, il contenuto della cella e il relativo layout. La classe implementa il [ `INativeElementView` ](xref:Xamarin.Forms.INativeElementView) interfaccia, che è obbligatorio quando il [ `ListView` ](xref:Xamarin.Forms.ListView) utilizza il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache. Questa interfaccia specifica che la classe deve implementare il [ `Element` ](xref:Xamarin.Forms.INativeElementView.Element) proprietà, che deve restituire i dati di cella personalizzato per le celle riciclate.

Il `NativeiOSCell` costruttore inizializza l'aspetto del `HeadingLabel`, `SubheadingLabel`, e `CellImageView` proprietà. Queste proprietà vengono usate per visualizzare i dati archiviati nel `NativeCell` istanza, con la `UpdateCell` metodo chiamato per impostare il valore di ogni proprietà. Inoltre, quando la [ `ListView` ](xref:Xamarin.Forms.ListView) utilizza il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) i dati visualizzati dalla strategia di memorizzazione nella cache il `HeadingLabel`, `SubheadingLabel`, e `CellImageView` le proprietà possono essere aggiornando il `OnNativeCellPropertyChanged` (metodo) nel programma di rendering personalizzata.

Viene eseguito il layout di cella per la `LayoutSubviews` esegue l'override, che imposta le coordinate del `HeadingLabel`, `SubheadingLabel`, e `CellImageView` all'interno della cella.

### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

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

Il `GetCellCore` metodo viene chiamato per compilare ogni cella da visualizzare. Ogni cella è una `NativeAndroidCell` istanza, che definisce il layout della cella e dei relativi dati. L'operazione dei `GetCellCore` metodo dipende il [ `ListView` ](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache:

- Quando la [ `ListView` ](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache viene [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement), il `GetCellCore` metodo verrà richiamato per ogni cella. Oggetto `NativeAndroidCell` verrà creato per ogni `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. Mentre l'utente scorre attraverso il `ListView`, `NativeAndroidCell` istanze verrà nuovamente usate. Per altre informazioni sulla cella Android usare di nuovo, vedere [riutilizzo delle visualizzazioni riga](~/android/user-interface/layouts/list-view/populating.md).

  > [!NOTE]
  > Si noti che questo codice di renderer personalizzato eseguirà alcune celle usare di nuovo, anche se il [ `ListView` ](xref:Xamarin.Forms.ListView) è impostata per conservare le celle.

  I dati visualizzati da ognuno `NativeAndroidCell` istanza, appena creata o usata nuovamente, verrà aggiornata con i dati da ognuno `NativeCell` dell'istanza dal `UpdateCell` (metodo).

  > [!NOTE]
  > Si noti che, anche se il `OnNativeCellPropertyChanged` metodo sarà richiamato quando il [ `ListView` ](xref:Xamarin.Forms.ListView) viene impostata per conservare le celle, non verrà aggiornato il `NativeAndroidCell` i valori delle proprietà.

- Quando la [ `ListView` ](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache viene [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement), il `GetCellCore` metodo verrà richiamato per ogni cella che viene inizialmente visualizzato sullo schermo. Oggetto `NativeAndroidCell` viene creata per ogni istanza `NativeCell` istanza che viene inizialmente visualizzato sullo schermo. I dati visualizzati da ognuno `NativeAndroidCell` verrà aggiornata con i dati da un'istanza di `NativeCell` istanza dal `UpdateCell` (metodo). Tuttavia, il `GetCellCore` metodo non verranno chiamato quando l'utente scorre il `ListView`. Al contrario, il `NativeAndroidCell` istanze verrà nuovamente usate.  `PropertyChanged` verranno generati eventi sul `NativeCell` modifica dei dati, di istanze e il `OnNativeCellPropertyChanged` gestore dell'evento verrà aggiornati i dati in ogni riusati `NativeAndroidCell` istanza.

Nell'esempio di codice riportato di seguito viene illustrato il `OnNativeCellPropertyChanged` metodo che ha richiamato quando un `PropertyChanged` viene generato l'evento:

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

Questo metodo aggiorna i dati visualizzati riusati `NativeAndroidCell` istanze. Viene eseguito un controllo per la proprietà che è stato modificato, come il metodo può essere chiamato più volte.

Il `NativeAndroidCell` classe definisce il layout per ogni cella e viene illustrato nell'esempio di codice seguente:

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

Questa classe definisce i controlli usati per eseguire il rendering, il contenuto della cella e il relativo layout. La classe implementa il [ `INativeElementView` ](xref:Xamarin.Forms.INativeElementView) interfaccia, che è obbligatorio quando il [ `ListView` ](xref:Xamarin.Forms.ListView) utilizza il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache. Questa interfaccia specifica che la classe deve implementare il [ `Element` ](xref:Xamarin.Forms.INativeElementView.Element) proprietà, che deve restituire i dati di cella personalizzato per le celle riciclate.

Il `NativeAndroidCell` costruttore ingrandisce il `NativeAndroidCell` layout e inizializza il `HeadingTextView`, `SubheadingTextView`, e `ImageView` proprietà ai controlli nel layout maggiori del necessario. Queste proprietà vengono usate per visualizzare i dati archiviati nel `NativeCell` istanza, con la `UpdateCell` metodo chiamato per impostare il valore di ogni proprietà. Inoltre, quando la [ `ListView` ](xref:Xamarin.Forms.ListView) utilizza il [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) i dati visualizzati dalla strategia di memorizzazione nella cache il `HeadingTextView`, `SubheadingTextView`, e `ImageView` le proprietà possono essere aggiornando il `OnNativeCellPropertyChanged` (metodo) nel programma di rendering personalizzata.

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

Questo layout specifica che due `TextView` controlli e un `ImageView` controllo consentono di visualizzare il contenuto della cella. I due `TextView` controlli sono orientati verticalmente all'interno di un `LinearLayout` controllo, con tutti i controlli sia indipendente all'interno di un `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creare il Renderer personalizzato nella piattaforma UWP

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeCell), typeof(NativeUWPCellRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPCellRenderer : ViewCellRenderer
    {
        public override Windows.UI.Xaml.DataTemplate GetTemplate(Cell cell)
        {
            return App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
        }
    }
}
```

Il `GetTemplate` metodo viene chiamato per restituire la cella da sottoporre a rendering per ogni riga di dati nell'elenco. Crea una `DataTemplate` per ognuno `NativeCell` istanza che verrà visualizzato sullo schermo, con la `DataTemplate` che definisce l'aspetto e il contenuto della cella.

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

Il `DataTemplate` specifica i controlli usati per visualizzare il contenuto della cella e il layout e l'aspetto. Due `TextBlock` controlli e un `Image` controllo consentono di visualizzare il contenuto della cella tramite associazione dati. Inoltre, un'istanza del `ConcatImageExtensionConverter` consente di concatenare il `.jpg` estensione file da ogni nome di file di immagine. Ciò garantisce che il `Image` controllo è possibile caricare ed eseguire il rendering dell'immagine quando è `Source` è impostata.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer personalizzato per un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) ospitato all'interno di un xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) controllo. Questo arresta i calcoli di layout di xamarin. Forms venga chiamato più volte durante `ListView` lo scorrimento.


## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
