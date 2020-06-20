---
title: Personalizzazione di un elemento ViewCell
description: Un Xamarin.Forms ViewCell è una cella che può essere aggiunta a un controllo ListView o Tableview che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come creare un renderer personalizzato per un ViewCell ospitato all'interno di un Xamarin.Forms controllo ListView.
ms.prod: xamarin
ms.assetid: 61F378C9-6DEF-436B-ACC3-2324B25D404E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c908da816352e8b3790ded0bef932e1485170abd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573872"
---
# <a name="customizing-a-viewcell"></a>Personalizzazione di un elemento ViewCell

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)

_Un Xamarin.Forms ViewCell è una cella che può essere aggiunta a un controllo ListView o Tableview che contiene una visualizzazione definita dallo sviluppatore. Questo articolo illustra come creare un renderer personalizzato per un ViewCell ospitato all'interno di un Xamarin.Forms controllo ListView. Questa operazione interrompe la Xamarin.Forms chiamata ripetuta dei calcoli di layout durante lo scorrimento di ListView._

Ogni Xamarin.Forms cella dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Quando un oggetto viene sottoposto a [`ViewCell`](xref:Xamarin.Forms.ViewCell) rendering da un' Xamarin.Forms applicazione, in iOS `ViewCellRenderer` viene creata un'istanza della classe, che a sua volta crea un'istanza di un `UITableViewCell` controllo nativo. Nella piattaforma Android la classe `ViewCellRenderer` crea un'istanza di un controllo `View` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `ViewCellRenderer` crea un'istanza di un controllo `DataTemplate` nativo. Per ulteriori informazioni sul renderer e sulle classi di controlli native che Xamarin.Forms controllano la mappa a, vedere [classi base renderer e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra [`ViewCell`](xref:Xamarin.Forms.ViewCell) e i controlli nativi corrispondenti che lo implementano:

![](viewcell-images/viewcell-classes.png "Relationship Between the ViewCell Control and the Implementing Native Controls")

Per implementare personalizzazioni specifiche della piattaforma, è possibile sfruttare il processo di rendering creando un renderer personalizzato per un oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#creating-the-custom-cell) una Xamarin.Forms cella personalizzata.
1. [Utilizzare](#consuming-the-custom-cell) la cella personalizzata da Xamarin.Forms .
1. [Creare](#creating-the-custom-renderer-on-each-platform) il renderer personalizzato per la cella in ogni piattaforma.

Ogni elemento verrà ora discusso a sua volta per implementare un `NativeCell` renderer che sfrutta un layout specifico della piattaforma per ogni cella ospitata all'interno di un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) controllo. Questa operazione interrompe la Xamarin.Forms chiamata ripetuta dei calcoli di layout durante lo `ListView` scorrimento.

## <a name="creating-the-custom-cell"></a>Creazione della cella personalizzata

È possibile creare un controllo cella personalizzato creando una sottoclasse della [`ViewCell`](xref:Xamarin.Forms.ViewCell) classe, come illustrato nell'esempio di codice seguente:

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

La classe `NativeCell` viene creata nel progetto di libreria .NET Standard e definisce l'API per la cella personalizzata. La cella personalizzata espone le proprietà `Name`, `Category` e `ImageFilename` che possono essere visualizzate usando il data binding. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

## <a name="consuming-the-custom-cell"></a>Uso della cella personalizzata

Per fare riferimento alla cella personalizzata `NativeCell` in XAML nel progetto di libreria .NET Standard, è possibile dichiarare uno spazio dei nomi per il percorso e usare il prefisso dello spazio dei nomi nell'elemento cella personalizzata. Nell'esempio di codice riportato di seguito viene illustrato come la cella personalizzata `NativeCell` può essere usata da una pagina XAML:

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

Il prefisso dello spazio dei nomi `local` può avere qualsiasi nome. I valori `clr-namespace` e `assembly` devono tuttavia corrispondere ai dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento alla cella personalizzata.

Nell'esempio di codice riportato di seguito viene illustrato come la cella personalizzata `NativeCell` può essere usata da una pagina C#:

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

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) Viene usato un controllo per visualizzare un elenco di dati, che viene popolato tramite la [`ItemSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) Proprietà. La [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache tenta di ridurre al minimo il `ListView` footprint di memoria e la velocità di esecuzione riutilizzando le celle dell'elenco. Per altre informazioni, vedere [Caching Strategy](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) (Strategia di memorizzazione nella cache).

Ogni riga dell'elenco contiene tre elementi di dati: un nome, una categoria e un nome di file di immagine. Il layout di ogni riga nell'elenco viene definito dall'oggetto a `DataTemplate` cui si fa riferimento tramite la [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà associabile. `DataTemplate` stabilisce che ogni riga di dati dell'elenco sia un elemento `NativeCell` che visualizza le relative proprietà `Name`, `Category` e `ImageFilename` usando il data binding. Per altre informazioni sul controllo `ListView`, vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

Un renderer personalizzato può essere ora aggiunto a ogni progetto di applicazione per personalizzare il layout specifico della piattaforma per ogni cella.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione del renderer personalizzato in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della classe `ViewCellRenderer` che esegue il rendering della cella personalizzata.
1. Eseguire l'override del metodo specifico della piattaforma che esegue il rendering della cella personalizzata e scrivere la logica per personalizzarlo.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzata per specificare che verrà usato per eseguire il rendering della Xamarin.Forms cella personalizzata. Questo attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms .

> [!NOTE]
> Per la maggior parte Xamarin.Forms degli elementi, è facoltativo fornire un renderer personalizzato in ogni progetto di piattaforma. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo. I renderer personalizzati sono tuttavia necessari in ogni progetto della piattaforma quando si esegue il rendering di un elemento [ViewCell](xref:Xamarin.Forms.ViewCell).

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](viewcell-images/solution-structure.png "NativeCell Custom Renderer Project Responsibilities")

Il rendering della cella `NativeCell` personalizzata viene eseguito dalle classi di renderer specifiche della piattaforma che derivano tutte dalla classe `ViewCellRenderer` per ogni piattaforma. Di conseguenza il rendering di ogni cella `NativeCell` personalizzata viene eseguito con il layout specifico della piattaforma, come illustrato negli screenshot seguenti:

![](viewcell-images/screenshots.png "NativeCell on each Platform")

La classe `ViewCellRenderer` espone i metodi specifici della piattaforma per il rendering della cella personalizzata, ovvero il metodo `GetCell` nella piattaforma iOS, il metodo `GetCellCore` nella piattaforma Android e il metodo `GetTemplate` nella piattaforma UWP.

Ogni classe renderer personalizzata è decorata con un `ExportRenderer` attributo che registra il renderer con Xamarin.Forms . L'attributo accetta due parametri: il nome del tipo della cella sottoposta a Xamarin.Forms rendering e il nome del tipo del renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione della classe di renderer personalizzato specifica di ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

Il metodo `GetCell` viene chiamato per compilare ogni cella da visualizzare. Ogni cella è un'istanza di `NativeiOSCell`, che definisce il layout della cella e dei relativi dati. L'operazione del `GetCell` metodo dipende dalla [`ListView`](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache:

- Quando la [`ListView`](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache è [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) , il `GetCell` metodo verrà richiamato per ogni cella. Viene creata un'istanza di `NativeiOSCell` per ogni istanza di `NativeCell` inizialmente visualizzata sullo schermo. Mentre l'utente scorre attraverso `ListView`, vengono nuovamente usate le istanze di `NativeiOSCell`. Per altre informazioni sul riutilizzo della cella iOS, vedere [Cell Reuse](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Riutilizzo della cella).

  > [!NOTE]
  > Questo codice renderer personalizzato esegue una riutilizzo delle celle anche quando [`ListView`](xref:Xamarin.Forms.ListView) è impostato per mantenere le celle.

  I dati visualizzati da ogni istanza di `NativeiOSCell`, appena creata o usata nuovamente, verranno aggiornati con i dati di ogni istanza di `NativeCell` dal metodo `UpdateCell`.

  > [!NOTE]
  > Il `OnNativeCellPropertyChanged` metodo non verrà mai richiamato quando la [`ListView`](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache è impostata per mantenere le celle.

- Quando la [`ListView`](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache è [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) , il `GetCell` metodo verrà richiamato per ogni cella visualizzata inizialmente sullo schermo. Viene creata un'istanza di `NativeiOSCell` per ogni istanza di `NativeCell` inizialmente visualizzata sullo schermo. I dati visualizzati da ogni istanza di `NativeiOSCell` verranno aggiornati con i dati dell'istanza di `NativeCell` dal metodo `UpdateCell`. Tuttavia, il metodo `GetCell` non viene richiamato quando l'utente scorre nell'oggetto `ListView`. Verranno invece usate nuovamente le istanze di `NativeiOSCell`. Gli eventi `PropertyChanged` verranno generati nell'istanza di `NativeCell` in caso di modifica dei dati e il gestore eventi `OnNativeCellPropertyChanged` aggiornerà i dati in ogni istanza di `NativeiOSCell` riusata.

L'esempio di codice riportato di seguito illustra il metodo `OnNativeCellPropertyChanged` richiamato quando viene generato un evento `PropertyChanged`:

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

Questo metodo aggiorna i dati visualizzati dalle istanze di `NativeiOSCell` riusate. Viene eseguito un controllo per la proprietà modificata, poiché il metodo può essere chiamato più volte.

La classe `NativeiOSCell` definisce il layout per ogni cella come illustra l'esempio di codice seguente:

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

Questa classe definisce i controlli usati per eseguire il rendering del contenuto della cella e il relativo layout. La classe implementa l' [`INativeElementView`](xref:Xamarin.Forms.INativeElementView) interfaccia, richiesta quando [`ListView`](xref:Xamarin.Forms.ListView) utilizza la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache. Questa interfaccia specifica che la classe deve implementare la [`Element`](xref:Xamarin.Forms.INativeElementView.Element) proprietà, che deve restituire i dati della cella personalizzata per le celle riciclate.

Il costruttore `NativeiOSCell` inizializza l'aspetto delle proprietà `HeadingLabel`, `SubheadingLabel` e `CellImageView`. Queste proprietà vengono usate per visualizzare i dati archiviati nell'istanza di `NativeCell`, con il metodo `UpdateCell` chiamato per impostare il valore di ogni proprietà. Inoltre, quando [`ListView`](xref:Xamarin.Forms.ListView) utilizza la strategia di [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) memorizzazione nella cache, i dati visualizzati dalle `HeadingLabel` proprietà, `SubheadingLabel` e `CellImageView` possono essere aggiornati dal `OnNativeCellPropertyChanged` metodo nel renderer personalizzato.

Il layout della cella viene eseguito dall'override di `LayoutSubviews`, che imposta le coordinate di `HeadingLabel`, `SubheadingLabel` e `CellImageView` all'interno della cella.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

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

Il metodo `GetCellCore` viene chiamato per compilare ogni cella da visualizzare. Ogni cella è un'istanza di `NativeAndroidCell`, che definisce il layout della cella e dei relativi dati. L'operazione del `GetCellCore` metodo dipende dalla [`ListView`](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache:

- Quando la [`ListView`](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache è [`RetainElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) , il `GetCellCore` metodo verrà richiamato per ogni cella. Viene creato un elemento `NativeAndroidCell` per ogni istanza di `NativeCell` inizialmente visualizzata sullo schermo. Mentre l'utente scorre attraverso `ListView`, vengono nuovamente usate le istanze di `NativeAndroidCell`. Per altre informazioni sul riutilizzo delle celle di Android, vedere [Row View Re-use](~/android/user-interface/layouts/list-view/populating.md) (Riutilizzo della vista righe).

  > [!NOTE]
  > Si noti che questo codice renderer personalizzato esegue una riutilizzo delle celle anche quando [`ListView`](xref:Xamarin.Forms.ListView) è impostato per mantenere le celle.

  I dati visualizzati da ogni istanza di `NativeAndroidCell`, appena creata o usata nuovamente, verranno aggiornati con i dati di ogni istanza di `NativeCell` dal metodo `UpdateCell`.

  > [!NOTE]
  > Si noti che, mentre il `OnNativeCellPropertyChanged` metodo viene richiamato quando [`ListView`](xref:Xamarin.Forms.ListView) è impostato su Mantieni celle, non aggiornerà i `NativeAndroidCell` valori della proprietà.

- Quando la [`ListView`](xref:Xamarin.Forms.ListView) strategia di memorizzazione nella cache è [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) , il `GetCellCore` metodo verrà richiamato per ogni cella visualizzata inizialmente sullo schermo. Viene creata un'istanza di `NativeAndroidCell` per ogni istanza di `NativeCell` inizialmente visualizzata sullo schermo. I dati visualizzati da ogni istanza di `NativeAndroidCell` verranno aggiornati con i dati dell'istanza di `NativeCell` dal metodo `UpdateCell`. Tuttavia, il metodo `GetCellCore` non viene richiamato quando l'utente scorre nell'oggetto `ListView`. Verranno invece usate nuovamente le istanze di `NativeAndroidCell`.  Gli eventi `PropertyChanged` verranno generati nell'istanza di `NativeCell` in caso di modifica dei dati e il gestore eventi `OnNativeCellPropertyChanged` aggiornerà i dati in ogni istanza di `NativeAndroidCell` riusata.

L'esempio di codice riportato di seguito illustra il metodo `OnNativeCellPropertyChanged` richiamato quando viene generato un evento `PropertyChanged`:

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

Questo metodo aggiorna i dati visualizzati dalle istanze di `NativeAndroidCell` riusate. Viene eseguito un controllo per la proprietà modificata, poiché il metodo può essere chiamato più volte.

La classe `NativeAndroidCell` definisce il layout per ogni cella come illustra l'esempio di codice seguente:

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

Questa classe definisce i controlli usati per eseguire il rendering del contenuto della cella e il relativo layout. La classe implementa l' [`INativeElementView`](xref:Xamarin.Forms.INativeElementView) interfaccia, richiesta quando [`ListView`](xref:Xamarin.Forms.ListView) utilizza la [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) strategia di memorizzazione nella cache. Questa interfaccia specifica che la classe deve implementare la [`Element`](xref:Xamarin.Forms.INativeElementView.Element) proprietà, che deve restituire i dati della cella personalizzata per le celle riciclate.

Il costruttore `NativeAndroidCell` ingrandisce il layout `NativeAndroidCell` e inizializza le proprietà `HeadingTextView`, `SubheadingTextView` e `ImageView` per i controlli nel layout ingrandito. Queste proprietà vengono usate per visualizzare i dati archiviati nell'istanza di `NativeCell`, con il metodo `UpdateCell` chiamato per impostare il valore di ogni proprietà. Inoltre, quando [`ListView`](xref:Xamarin.Forms.ListView) utilizza la strategia di [`RecycleElement`](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) memorizzazione nella cache, i dati visualizzati dalle `HeadingTextView` proprietà, `SubheadingTextView` e `ImageView` possono essere aggiornati dal `OnNativeCellPropertyChanged` metodo nel renderer personalizzato.

L'esempio di codice seguente illustra la definizione del layout per il file di layout `NativeAndroidCell.axml`:

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

Questo layout specifica che due controlli `TextView` e un controllo `ImageView` vengono usati per visualizzare il contenuto della cella. I due controlli `TextView` sono orientati verticalmente all'interno di un controllo `LinearLayout` e tutti i controlli sono contenuti in un `RelativeLayout`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creazione del renderer personalizzato in UWP

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

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

Il metodo `GetTemplate` viene chiamato per restituire la cella da sottoporre a rendering per ogni riga di dati dell'elenco. Crea un elemento `DataTemplate` per ogni istanza di `NativeCell` che verrà visualizzata sullo schermo, con `DataTemplate` che definisce l'aspetto e il contenuto della cella.

`DataTemplate` è archiviato nel dizionario risorse a livello di applicazione, come illustrato nell'esempio di codice seguente:

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

`DataTemplate` specifica i controlli usati per visualizzare il contenuto della cella, nonché il layout e l'aspetto. Due controlli `TextBlock` e un controllo `Image` vengono usati per visualizzare il contenuto della cella usando il data binding. Inoltre, viene usata un'istanza di `ConcatImageExtensionConverter` per concatenare l'estensione file `.jpg` a ogni nome di file di immagine. Ciò garantisce che il controllo `Image` sia in grado di caricare l'immagine ed eseguirne il rendering quando è impostata la relativa proprietà `Source`.

## <a name="summary"></a>Summary

Questo articolo ha illustrato come creare un renderer personalizzato per un oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) ospitato all'interno di un Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) controllo. Questa operazione interrompe la Xamarin.Forms chiamata ripetuta dei calcoli di layout durante lo `ListView` scorrimento.

## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [CustomRendererViewCell (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
