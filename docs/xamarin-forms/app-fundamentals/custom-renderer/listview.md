---
title: Personalizzazione di un controllo ListView
description: Un controllo ListView di xamarin. Forms è una visualizzazione contenente una raccolta di dati come un elenco verticale. In questo articolo viene illustrato come creare un renderer personalizzato che incapsula i controlli elenco specifico della piattaforma e il layout di cella native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo.
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 69640c1cdea6d7dbe3ec82dacbc77991c7b28c99
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847400"
---
# <a name="customizing-a-listview"></a>Personalizzazione di un controllo ListView

_Un controllo ListView di xamarin. Forms è una visualizzazione contenente una raccolta di dati come un elenco verticale. In questo articolo viene illustrato come creare un renderer personalizzato che incapsula i controlli elenco specifico della piattaforma e il layout di cella native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo._

Tutte le viste di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) viene eseguito il rendering da un'applicazione di xamarin. Forms, in iOS il `ListViewRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `UITableView` controllo. Nella piattaforma Android, il `ListViewRenderer` un'istanza nativa `ListView` controllo. Nella piattaforma UWP (Universal Windows), il `ListViewRenderer` classe viene creata un'istanza nativa `ListView` controllo. Per ulteriori informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controllo e i corrispondenti controlli nativi che l'implementano:

![](listview-images/listview-classes.png "Relazione tra il controllo ListView e i controlli nativi di implementazione")

Il processo di rendering può essere sfruttato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_ListView_Control) un controllo personalizzato di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento verrà ora essere descritti in dettaglio, per implementare un `NativeListView` renderer che consente di sfruttare i controlli elenco specifico della piattaforma e il layout di cella nativo. Questo scenario è utile durante l'importazione di un app nativa esistente che contiene l'elenco e un codice di celle che può essere utilizzato nuovamente. Inoltre, consente dettagliate di personalizzazione delle funzionalità di controllo elenco che può influire sulle prestazioni, ad esempio la virtualizzazione dei dati.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Creazione del controllo ListView personalizzato

Un oggetto personalizzato [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controllo può essere creato creando sottoclassi di `ListView` classe, come illustrato nell'esempio di codice seguente:

```csharp
public class NativeListView : ListView
{
  public static readonly BindableProperty ItemsProperty =
    BindableProperty.Create ("Items", typeof(IEnumerable<DataSource>), typeof(NativeListView), new List<DataSource> ());

  public IEnumerable<DataSource> Items {
    get { return (IEnumerable<DataSource>)GetValue (ItemsProperty); }
    set { SetValue (ItemsProperty, value); }
  }

  public event EventHandler<SelectedItemChangedEventArgs> ItemSelected;

  public void NotifyItemSelected (object item)
  {
    if (ItemSelected != null) {
      ItemSelected (this, new SelectedItemChangedEventArgs (item));
    }
  }
}
```

Il `NativeListView` viene creato nel progetto di libreria Standard di .NET e definisce l'API per il controllo personalizzato. Questo controllo espone un `Items` proprietà che viene utilizzato per popolare il `ListView` con i dati e che può essere associato per dati ai fini della visualizzazione. Espone inoltre un `ItemSelected` evento generato ogni volta che viene selezionato un elemento in un controllo elenco native specifiche della piattaforma. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Utilizzo del controllo personalizzato

Il `NativeListView` controllo personalizzato può fare riferimento in Xaml nel progetto di libreria .NET Standard dichiarare uno spazio dei nomi per la posizione e utilizzando il prefisso dello spazio dei nomi nel controllo. Nell'esempio di codice riportato di seguito viene illustrato come la `NativeListView` controllo personalizzato può essere utilizzato da una pagina XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <ContentPage.Content>
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*" />
            </Grid.RowDefinitions>
          <Label Text="{x:Static local:App.Description}" HorizontalTextAlignment="Center" />
            <local:NativeListView Grid.Row="1" x:Name="nativeListView" ItemSelected="OnItemSelected" VerticalOptions="FillAndExpand" />
          </Grid>
      </ContentPage.Content>
</ContentPage>
```

Il `local` prefisso dello spazio dei nomi denominato nulla. Tuttavia, il `clr-namespace` e `assembly` i valori devono corrispondere i dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come la `NativeListView` controllo personalizzato può essere utilizzato da una pagina in c#:

```csharp
public class MainPageCS : ContentPage
{
    NativeListView nativeListView;

    public MainPageCS()
    {
        nativeListView = new NativeListView
        {
            Items = DataSource.GetList(),
            VerticalOptions = LayoutOptions.FillAndExpand
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

        Content = new Grid
        {
            RowDefinitions = {
                new RowDefinition { Height = GridLength.Auto },
                new RowDefinition { Height = new GridLength (1, GridUnitType.Star) }
            },
            Children = {
                new Label { Text = App.Description, HorizontalTextAlignment = TextAlignment.Center },
                nativeListView
            }
        };
        nativeListView.ItemSelected += OnItemSelected;
    }
    ...
}
```

Il `NativeListView` controllo personalizzato utilizza renderer personalizzato specifico della piattaforma per visualizzare un elenco di dati, che viene compilati tramite il `Items` proprietà. Ogni riga nell'elenco contiene tre elementi di dati, un nome, una categoria e un nome di file di immagine. Il layout di ogni riga nell'elenco viene definito dal renderer personalizzato specifico della piattaforma.

> [!NOTE]
> Poiché il `NativeListView` verrà visualizzato un controllo personalizzato utilizzando i controlli elenco specifico della piattaforma che includono lo scorrimento di capacità, il controllo personalizzato non deve trovarsi nei controlli di layout scorrevole, ad esempio il [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/).

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per creare layout di cella nativo e i controlli elenco specifico della piattaforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione di Renderer personalizzato a seconda della piattaforma

Il processo di creazione della classe renderer personalizzato è come segue:

1. Creare una sottoclasse di `ListViewRenderer` classe che esegue il rendering del controllo personalizzato.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering di controllo e scrivere la logica personalizzata per personalizzarlo. Questo metodo viene chiamato quando il corrispondente xamarin. Forms [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) viene creato.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering del controllo personalizzato di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> È facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, verrà utilizzato il renderer predefinito per la classe di base della cella.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](listview-images/solution-structure.png "Responsabilità di progetto personalizzato NativeListView Renderer")

Il `NativeListView` viene eseguito il rendering di controlli personalizzati dalle classi renderer specifico della piattaforma, che derivano dalla `ListViewRenderer` classe per ogni piattaforma. Di conseguenza, ogni `NativeListView` controllo personalizzato viene eseguito il rendering con controlli elenco di specifiche della piattaforma e layout delle celle nativo, come illustrato nelle schermate seguenti:

![](listview-images/screenshots.png "NativeListView in ciascuna piattaforma")

Il `ListViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms per il rendering del controllo native corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro, che contiene `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* associata rispettivamente. Nell'applicazione di esempio, il `OldElement` proprietà sarà `null` e `NewElement` proprietà contiene un riferimento al `NativeListView` istanza.

Una versione sottoposta a override del `OnElementChanged` (metodo), in ogni classe renderer specifico della piattaforma, è possibile eseguire la personalizzazione del controllo nativo. Un riferimento tipizzato al controllo nativo utilizzato nella piattaforma sono accessibili tramite il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

Prestare attenzione quando si sottoscrive i gestori eventi nel `OnElementChanged` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the native control and subscribe to event handlers
  }
}
```

Deve essere configurato solo controllo nativo e i gestori eventi sottoscritti quando è collegato il renderer personalizzato a un nuovo elemento di xamarin. Forms. Analogamente, gestori di eventi che sono stati sottoscritti devono essere annullati da solo quando l'elemento del renderer è associato alle modifiche. Questo approccio consente di creare un renderer personalizzato che non subisca delle perdite di memoria.

Una versione sottoposta a override del `OnElementPropertyChanged` (metodo), in ogni classe renderer specifico della piattaforma, è possibile rispondere alle modifiche di proprietà associabile del controllo personalizzato di xamarin. Forms. Un controllo per la proprietà che è stato modificato deve essere sempre effettuato, come la sostituzione può essere chiamata più volte.

Ogni classe renderer personalizzato è decorato con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo si applica all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma iOS:

```csharp
[assembly: ExportRenderer (typeof(NativeListView), typeof(NativeiOSListViewRenderer))]
namespace CustomRenderer.iOS
{
    public class NativeiOSListViewRenderer : ListViewRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                // Unsubscribe
            }

            if (e.NewElement != null) {
                Control.Source = new NativeiOSListViewSource (e.NewElement as NativeListView);
            }
        }
    }
}
```

Il `UITableView` controllo viene configurato tramite la creazione di un'istanza di `NativeiOSListViewSource` classe, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms. Questa classe fornisce i dati per il `UITableView` controllo eseguendo l'override di `RowsInSection` e `GetCell` metodi il `UITableViewSource` classe e l'esposizione di un `Items` proprietà che contiene l'elenco di dati da visualizzare. La classe fornisce anche un `RowSelected` override del metodo che richiama la `ItemSelected` evento fornito dal `NativeListView` controllo personalizzato. Per ulteriori informazioni sul metodo di override, vedere [UITableViewSource sottoclassi](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). Il `GetCell` metodo restituisce un `UITableCellView` che viene popolata con i dati per ogni riga nell'elenco e viene illustrato nell'esempio di codice seguente:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
  // request a recycled cell to save memory
  NativeiOSListViewCell cell = tableView.DequeueReusableCell (cellIdentifier) as NativeiOSListViewCell;

  // if there are no cells to reuse, create a new one
  if (cell == null) {
    cell = new NativeiOSListViewCell (cellIdentifier);
  }

  if (String.IsNullOrWhiteSpace (tableItems [indexPath.Row].ImageFilename)) {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , null);
  } else {
    cell.UpdateCell (tableItems [indexPath.Row].Name
      , tableItems [indexPath.Row].Category
      , UIImage.FromFile ("Images/" + tableItems [indexPath.Row].ImageFilename + ".jpg"));
  }

  return cell;
}
```

Questo metodo crea un `NativeiOSListViewCell` istanza per ogni riga di dati che verranno visualizzati sullo schermo. Il `NativeiOSCell` istanza definisce il layout di ogni cella e i dati della cella. Quando una cella viene rimosso dallo schermo a causa di scorrimento, la cella sarà disponibile per il riutilizzo. Questo evita di sprecare memoria assicurando che sono presenti solo `NativeiOSCell` istanze per i dati verrà visualizzati la schermata, anziché tutti i dati nell'elenco. Per ulteriori informazioni sul riutilizzo di cella, vedere [cella riutilizzare](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). Il `GetCell` metodo consente inoltre di leggere il `ImageFilename` proprietà di ogni riga di dati, che sia presente e legge l'immagine e viene archiviato come un `UIImage` istanza, prima di aggiornare il `NativeiOSListViewCell` istanza con i dati (nome, categoria e immagine) per la riga.

La `NativeiOSListViewCell` classe definisce il layout per ogni cella e viene illustrato nell'esempio di codice seguente:

```csharp
public class NativeiOSListViewCell : UITableViewCell
{
  UILabel headingLabel, subheadingLabel;
  UIImageView imageView;

  public NativeiOSListViewCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
  {
    SelectionStyle = UITableViewCellSelectionStyle.Gray;

    ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);

    imageView = new UIImageView ();

    headingLabel = new UILabel () {
      Font = UIFont.FromName ("Cochin-BoldItalic", 22f),
      TextColor = UIColor.FromRGB (127, 51, 0),
      BackgroundColor = UIColor.Clear
    };

    subheadingLabel = new UILabel () {
      Font = UIFont.FromName ("AmericanTypewriter", 12f),
      TextColor = UIColor.FromRGB (38, 127, 0),
      TextAlignment = UITextAlignment.Center,
      BackgroundColor = UIColor.Clear
    };

    ContentView.Add (headingLabel);
    ContentView.Add (subheadingLabel);
    ContentView.Add (imageView);
  }

  public void UpdateCell (string caption, string subtitle, UIImage image)
  {
    headingLabel.Text = caption;
    subheadingLabel.Text = subtitle;
    imageView.Image = image;
  }

  public override void LayoutSubviews ()
  {
    base.LayoutSubviews ();

    headingLabel.Frame = new CoreGraphics.CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
    subheadingLabel.Frame = new CoreGraphics.CGRect (100, 18, 100, 20);
    imageView.Frame = new CoreGraphics.CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
  }
}
```

Questa classe definisce i controlli utilizzati per eseguire il rendering, il contenuto della cella e il layout. Il `NativeiOSListViewCell` costruttore crea istanze di `UILabel` e `UIImageView` controlla e inizializza il loro aspetto. Questi controlli consentono di visualizzare i dati di ogni riga, con la `UpdateCell` il metodo utilizzato per impostare questi dati nel `UILabel` e `UIImageView` istanze. Il percorso di queste istanze viene impostato da sottoposto a override `LayoutSubviews` metodo, specificando le coordinate della cella.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risponde a una modifica di proprietà del controllo personalizzato

Se il `NativeListView.Items` proprietà viene modificato, a causa dell'aggiunta di elementi o rimosso dall'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Questo può essere effettuato eseguendo l'override di `OnElementPropertyChanged` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

Il metodo crea una nuova istanza del `NativeiOSListViewSource` classe che fornisce dati per il `UITableView` di controllo, a condizione che le proprietà associabili `NativeListView.Items` proprietà è stata modificata.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma Android:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeAndroidListViewRenderer))]
namespace CustomRenderer.Droid
{
    public class NativeAndroidListViewRenderer : ListViewRenderer
    {
        Context _context;

        public NativeAndroidListViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // unsubscribe
                Control.ItemClick -= OnItemClick;
            }

            if (e.NewElement != null)
            {
                // subscribe
                Control.Adapter = new NativeAndroidListViewAdapter(_context as Android.App.Activity, e.NewElement as NativeListView);
                Control.ItemClick += OnItemClick;
            }
        }
        ...

        void OnItemClick(object sender, Android.Widget.AdapterView.ItemClickEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(((NativeListView)Element).Items.ToList()[e.Position - 1]);
        }
    }
}
```

L'oggetto nativo `ListView` controllo viene configurato a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms. Questa configurazione prevede la creazione di un'istanza del `NativeAndroidListViewAdapter` classe che fornisce dati per l'oggetto nativo `ListView` controllare e registrare un gestore eventi per l'elaborazione di `ItemClick` evento. A sua volta richiama il gestore di `ItemSelected` evento fornito dal `NativeListView` controllo personalizzato. Il `ItemClick` evento è annullato la sottoscrizione a, se l'elemento di xamarin. Forms, il renderer è associato alle modifiche.

Il `NativeAndroidListViewAdapter` deriva il `BaseAdapter` classe ed espone un `Items` proprietà che contiene l'elenco di dati da visualizzare, nonché eseguire l'override di `Count`, `GetView`, `GetItemId`, e `this[int]` metodi. Per ulteriori informazioni su questi override del metodo, vedere [implementa un ListAdapter](~/android/user-interface/layouts/list-view/populating.md). Il `GetView` metodo restituisce una visualizzazione per ogni riga, popolato con i dati e viene illustrato nell'esempio di codice seguente:

```csharp
public override View GetView (int position, View convertView, ViewGroup parent)
{
  var item = tableItems [position];

  var view = convertView;
  if (view == null) {
    // no view to re-use, create new
    view = context.LayoutInflater.Inflate (Resource.Layout.NativeAndroidListViewCell, null);
  }
  view.FindViewById<TextView> (Resource.Id.Text1).Text = item.Name;
  view.FindViewById<TextView> (Resource.Id.Text2).Text = item.Category;

  // grab the old image and dispose of it
  if (view.FindViewById<ImageView> (Resource.Id.Image).Drawable != null) {
    using (var image = view.FindViewById<ImageView> (Resource.Id.Image).Drawable as BitmapDrawable) {
      if (image != null) {
        if (image.Bitmap != null) {
          //image.Bitmap.Recycle ();
          image.Bitmap.Dispose ();
        }
      }
    }
  }

  // If a new image is required, display it
  if (!String.IsNullOrWhiteSpace (item.ImageFilename)) {
    context.Resources.GetBitmapAsync (item.ImageFilename).ContinueWith ((t) => {
      var bitmap = t.Result;
      if (bitmap != null) {
        view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (bitmap);
        bitmap.Dispose ();
      }
    }, TaskScheduler.FromCurrentSynchronizationContext ());
  } else {
    // clear the image
    view.FindViewById<ImageView> (Resource.Id.Image).SetImageBitmap (null);
  }

  return view;
}
```

Il `GetView` metodo viene chiamato per restituire la cella da sottoporre a rendering come un `View`, per ogni riga di dati nell'elenco. Crea un `View` istanza per ogni riga di dati che verranno visualizzati sullo schermo, con l'aspetto del `View` istanza viene definito in un file di layout. Quando una cella viene rimosso dallo schermo a causa di scorrimento, la cella sarà disponibile per il riutilizzo. Questo evita di sprecare memoria assicurando che sono presenti solo `View` istanze per i dati verrà visualizzati la schermata, anziché tutti i dati nell'elenco. Per ulteriori informazioni sul riutilizzo di visualizzazione, vedere [riga visualizzazione usare nuovamente](~/android/user-interface/layouts/list-view/populating.md).

Il `GetView` metodo compila inoltre il `View` istanza con dati, inclusa la lettura dei dati di immagine dal nome del file specificato nella `ImageFilename` proprietà.

Il layout di ogni cella di dispayed da nativo `ListView` è definito nel `NativeAndroidListViewCell.axml` file di layout, che viene ingrandita in base il `LayoutInflater.Inflate` metodo. Esempio di codice seguente viene illustrata la definizione del layout:

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
            android:id="@+id/Text1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#FF7F3300"
            android:textSize="20dip"
            android:textStyle="italic" />
        <TextView
            android:id="@+id/Text2"
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risponde a una modifica di proprietà del controllo personalizzato

Se il `NativeListView.Items` proprietà viene modificato, a causa dell'aggiunta di elementi o rimosso dall'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Questo può essere effettuato eseguendo l'override di `OnElementPropertyChanged` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

Il metodo crea una nuova istanza di `NativeAndroidListViewAdapter` classe che fornisce dati per l'oggetto nativo `ListView` di controllo, a condizione che le proprietà associabili `NativeListView.Items` proprietà è stata modificata.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creazione di Renderer personalizzato in UWP

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma UWP:

```csharp
[assembly: ExportRenderer(typeof(NativeListView), typeof(NativeUWPListViewRenderer))]
namespace CustomRenderer.UWP
{
    public class NativeUWPListViewRenderer : ListViewRenderer
    {
        ListView listView;

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.ListView> e)
        {
            base.OnElementChanged(e);

            listView = Control as ListView;

            if (e.OldElement != null)
            {
                // Unsubscribe
                listView.SelectionChanged -= OnSelectedItemChanged;
            }

            if (e.NewElement != null)
            {
                listView.SelectionMode = ListViewSelectionMode.Single;
                listView.IsItemClickEnabled = false;
                listView.ItemsSource = ((NativeListView)e.NewElement).Items;             
                listView.ItemTemplate = App.Current.Resources["ListViewItemTemplate"] as Windows.UI.Xaml.DataTemplate;
                // Subscribe
                listView.SelectionChanged += OnSelectedItemChanged;
            }  
        }

        void OnSelectedItemChanged(object sender, SelectionChangedEventArgs e)
        {
            ((NativeListView)Element).NotifyItemSelected(listView.SelectedItem);
        }
    }
}
```

L'oggetto nativo `ListView` controllo viene configurato a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms. Questa configurazione prevede l'impostazione modalità nativa `ListView` controllo risponderà agli elementi selezionati, popolamento dei dati visualizzato dal controllo, definire l'aspetto e il contenuto di ogni cella e la registrazione di un gestore eventi per l'elaborazione di `SelectionChanged` evento. A sua volta richiama il gestore di `ItemSelected` evento fornito dal `NativeListView` controllo personalizzato. Il `SelectionChanged` evento è annullato la sottoscrizione a, se l'elemento di xamarin. Forms, il renderer è associato alle modifiche.

L'aspetto e il contenuto di ogni nativo `ListView` cella sono definiti da un `DataTemplate` denominato `ListViewItemTemplate`. Questo `DataTemplate` vengono archiviati nel dizionario risorse a livello di applicazione e viene illustrato nell'esempio di codice seguente:

```xaml
<DataTemplate x:Key="ListViewItemTemplate">
    <Grid Background="#DAFF7F">
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

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risponde a una modifica di proprietà del controllo personalizzato

Se il `NativeListView.Items` proprietà viene modificato, a causa dell'aggiunta di elementi o rimosso dall'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Questo può essere effettuato eseguendo l'override di `OnElementPropertyChanged` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
    base.OnElementPropertyChanged(sender, e);

    if (e.PropertyName == NativeListView.ItemsProperty.PropertyName)
    {
        listView.ItemsSource = ((NativeListView)Element).Items;
    }
}
```

Il metodo popola nuovamente l'oggetto nativo `ListView` controllo con i dati modificati, a condizione che le proprietà associabili `NativeListView.Items` proprietà è stata modificata.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare un renderer personalizzato che incapsula i controlli elenco specifico della piattaforma e il layout di cella native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererListView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/listview/)
