---
title: ''
description: Xamarin.FormsListView è una vista in cui viene visualizzata una raccolta di dati sotto forma di elenco verticale. Questo articolo spiega come creare un renderer personalizzato che incapsula gli elenchi specifici della piattaforma e i layout di cella nativi, consentendo un maggiore controllo sulle prestazioni dei controlli nativi.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3403948c2853289610a73bb36073f09c0c86137d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135304"
---
# <a name="customizing-a-listview"></a>Personalizzazione di un oggetto ListView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)

_Xamarin.FormsListView è una vista in cui viene visualizzata una raccolta di dati sotto forma di elenco verticale. Questo articolo illustra come creare un renderer personalizzato che incapsula i controlli elenco specifici della piattaforma e i layout delle celle native, consentendo un maggiore controllo sulle prestazioni del controllo elenco nativo._

Ogni Xamarin.Forms visualizzazione dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Quando un oggetto viene sottoposto a [`ListView`](xref:Xamarin.Forms.ListView) rendering da un' Xamarin.Forms applicazione, in iOS `ListViewRenderer` viene creata un'istanza della classe, che a sua volta crea un'istanza di un `UITableView` controllo nativo. Nella piattaforma Android la classe `ListViewRenderer` crea un'istanza di un controllo `ListView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `ListViewRenderer` crea un'istanza di un controllo `ListView` nativo. Per ulteriori informazioni sul renderer e sulle classi di controlli native che Xamarin.Forms controllano la mappa a, vedere [classi base renderer e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [`ListView`](xref:Xamarin.Forms.ListView) controllo e i controlli nativi corrispondenti che lo implementano:

![](listview-images/listview-classes.png "Relationship Between the ListView Control and the Implementing Native Controls")

Per implementare personalizzazioni specifiche della piattaforma, è possibile sfruttare il processo di rendering creando un renderer personalizzato per un oggetto [`ListView`](xref:Xamarin.Forms.ListView) in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Custom_ListView_Control) un Xamarin.Forms controllo personalizzato.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da Xamarin.Forms .
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento verrà trattato separatamente, per implementare un renderer `NativeListView` che si avvale di elenchi specifici della piattaforma e di layout di cella nativi. Questo scenario è utile quando si trasferisce un'app nativa esistente che contiene codice di elenco e cella che può essere nuovamente usato. Inoltre, consente di personalizzare in modo dettagliato le funzionalità di elenco che possono influire sulle prestazioni, ad esempio la virtualizzazione dei dati.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Creazione di un controllo ListView personalizzato

[`ListView`](xref:Xamarin.Forms.ListView)È possibile creare un controllo personalizzato creando una sottoclasse della `ListView` classe, come illustrato nell'esempio di codice seguente:

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

L'elemento `NativeListView` viene creato nel progetto di libreria .NET Standard e definisce l'API per il controllo personalizzato. Il controllo espone una proprietà `Items` che viene usata per popolare `ListView` con i dati e può essere associata a dati ai fini della visualizzazione. Espone inoltre un evento `ItemSelected` che verrà attivato ogni volta che viene selezionato un elemento in un elenco nativo specifico della piattaforma. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Uso del controllo personalizzato

Per fare riferimento al controllo personalizzato `NativeListView` in XAML nel progetto di libreria .NET Standard, è possibile dichiarare uno spazio dei nomi per il percorso e usare il prefisso dello spazio dei nomi nel controllo. Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `NativeListView` può essere usato da una pagina XAML:

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

Il prefisso dello spazio dei nomi `local` può avere qualsiasi nome. I valori `clr-namespace` e `assembly` devono tuttavia corrispondere ai dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il controllo personalizzato `NativeListView` può essere usato da una pagina C#:

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

Il controllo personalizzato `NativeListView` usa renderer personalizzati specifici della piattaforma per visualizzare un elenco di dati, che viene popolato usando la proprietà `Items`. Ogni riga dell'elenco contiene tre elementi di dati: un nome, una categoria e un nome di file di immagine. Il layout di ogni riga dell'elenco è definito dal renderer personalizzato specifico della piattaforma.

> [!NOTE]
> Poiché `NativeListView` verrà eseguito il rendering del controllo personalizzato utilizzando controlli elenco specifici della piattaforma che includono funzionalità di scorrimento, il controllo personalizzato non deve essere ospitato nei controlli di layout scorrevoli, ad esempio [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per creare elenchi specifici della piattaforma e layout di cella nativi.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione del renderer personalizzato in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della classe `ListViewRenderer` che esegue il rendering del controllo personalizzato.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering del controllo personalizzato e scrivere la logica per personalizzarlo. Questo metodo viene chiamato quando viene creato l'oggetto corrispondente Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) .
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzata per specificare che verrà usato per eseguire il rendering del Xamarin.Forms controllo personalizzato. Questo attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms .

> [!NOTE]
> La specifica di un renderer personalizzato nel progetto di ogni piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base della cella.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](listview-images/solution-structure.png "NativeListView Custom Renderer Project Responsibilities")

Il rendering del controllo personalizzato `NativeListView` viene eseguito dalle classi di renderer specifiche della piattaforma che derivano tutte dalla classe `ListViewRenderer` per ogni piattaforma. Di conseguenza il rendering di ogni controllo personalizzato `NativeListView` viene eseguito con elenchi specifici della piattaforma e layout di cella nativi, come illustrato negli screenshot seguenti:

![](listview-images/screenshots.png "NativeListView on each Platform")

La `ListViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando Xamarin.Forms viene creato il controllo personalizzato per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano l' Xamarin.Forms elemento a cui è *stato* collegato il renderer e l' Xamarin.Forms elemento a cui *è* collegato rispettivamente il renderer. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento all'istanza di `NativeListView`.

La personalizzazione del controllo nativo deve essere eseguita in una versione sostituita del metodo `OnElementChanged` in ogni classe di renderer specifica della piattaforma. Un riferimento tipizzato al controllo nativo usato nella piattaforma è accessibile attraverso la proprietà `Control`. Inoltre, è possibile ottenere un riferimento al Xamarin.Forms controllo di cui è in corso il rendering tramite la `Element` Proprietà.

Prestare attenzione quando si sottoscrivono i gestori degli eventi nel metodo `OnElementChanged`, come illustrato nell'esempio di codice seguente:

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

Il controllo nativo deve essere configurato solo e i gestori di eventi hanno sottoscritto quando il renderer personalizzato è associato a un nuovo Xamarin.Forms elemento. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. L'adozione di questo approccio consente di creare un renderer personalizzato che non subisce perdite di memoria.

Una versione sottoposta a override del `OnElementPropertyChanged` metodo, in ogni classe renderer specifica della piattaforma, è la posizione in cui rispondere alle modifiche delle proprietà associabili sul Xamarin.Forms controllo personalizzato. Deve sempre essere eseguito un controllo della proprietà modificata, dal momento che l'override può essere chiamato più volte.

Ogni classe renderer personalizzata è decorata con un `ExportRenderer` attributo che registra il renderer con Xamarin.Forms . L'attributo accetta due parametri: il nome del tipo del Xamarin.Forms controllo personalizzato di cui viene eseguito il rendering e il nome del tipo del renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione della classe di renderer personalizzato specifica di ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

Il `UITableView` controllo viene configurato creando un'istanza della `NativeiOSListViewSource` classe, purché il renderer personalizzato sia collegato a un nuovo Xamarin.Forms elemento. Questa classe specifica i dati per il controllo `UITableView` eseguendo l'override dei metodi `RowsInSection` e `GetCell` della classe `UITableViewSource` ed esponendo una proprietà `Items` che contiene l'elenco dei dati da visualizzare. La classe determina anche l'override del metodo `RowSelected` che richiama l'evento `ItemSelected` specificato dal controllo personalizzato `NativeListView`. Per altre informazioni sull'override dei metodi, vedere [Subclassing UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). Il metodo `GetCell` restituisce un elemento `UITableCellView` che viene popolato con i dati per ogni riga dell'elenco, come illustra l'esempio di codice seguente:

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

Questo metodo crea un'istanza di `NativeiOSListViewCell` per ogni riga di dati che verranno visualizzati sullo schermo. L'istanza di `NativeiOSCell` definisce il layout di ogni cella e i dati della cella. Quando una cella scompare dallo schermo a causa dello scorrimento, la cella viene resa disponibile per il riutilizzo. Questo evita di sprecare memoria e garantisce che siano presenti istanze di `NativeiOSCell` solo per i dati visualizzati sullo schermo e non per tutti i dati dell'elenco. Per altre informazioni sul riutilizzo della cella, vedere [Cell Reuse](~/ios/user-interface/controls/tables/populating-a-table-with-data.md) (Riutilizzo della cella). Il metodo `GetCell` legge anche la proprietà `ImageFilename` di ogni riga di dati, se esiste, legge l'immagine e la archivia come istanza di `UIImage`, prima di aggiornare l'istanza di `NativeiOSListViewCell` con i dati (nome, categoria e immagine) per la riga.

La classe `NativeiOSListViewCell` definisce il layout per ogni cella come illustra l'esempio di codice seguente:

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

Questa classe definisce i controlli usati per eseguire il rendering del contenuto della cella e il relativo layout. Il costruttore `NativeiOSListViewCell` crea istanze dei controlli `UILabel` e `UIImageView` e ne inizializza l'aspetto. Questi controlli vengono usati per visualizzare i dati di ogni riga e il metodo `UpdateCell` viene usato per impostare questi dati nelle istanze di `UILabel` e `UIImageView`. Il percorso di queste istanze viene impostato dal metodo `LayoutSubviews` sottoposto a override specificando le coordinate all'interno della cella.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risposta alla modifica di una proprietà per il controllo personalizzato

Se la proprietà `NativeListView.Items` cambia a causa dell'aggiunta o rimozione di elementi nell'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Per ottenere questo risultato, eseguire l'override del metodo `OnElementPropertyChanged`, come illustra l'esempio di codice seguente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

Il metodo crea una nuova istanza della classe `NativeiOSListViewSource` che specifica i dati per il controllo `UITableView`, a condizione che venga modificata la proprietà `NativeListView.Items` associabile.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

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

Il `ListView` controllo nativo viene configurato purché il renderer personalizzato sia collegato a un nuovo Xamarin.Forms elemento. Questa configurazione prevede la creazione di un'istanza della classe `NativeAndroidListViewAdapter` che specifica i dati per l'oggetto nativo `ListView` e la registrazione di un gestore eventi per elaborare l'evento `ItemClick`. A sua volta, il gestore richiamerà l'evento `ItemSelected` specificato dal controllo personalizzato `NativeListView`. Viene annullata la `ItemClick` sottoscrizione dell'evento se l' Xamarin.Forms elemento a cui è associato il renderer viene modificato.

`NativeAndroidListViewAdapter` deriva dalla classe `BaseAdapter` ed espone una proprietà `Items` che contiene l'elenco dei dati da visualizzare, eseguendo anche l'override dei metodi `Count`, `GetView`, `GetItemId` e `this[int]`. Per altre informazioni sull'override di questi metodi, vedere [Implementing a ListAdapter](~/android/user-interface/layouts/list-view/populating.md) (Implementazione di ListAdapter). Il metodo `GetView` restituisce una vista per ogni riga, popolata con dati, come illustra l'esempio di codice seguente:

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

Il metodo `GetView` viene chiamato per restituire la cella da sottoporre a rendering, come `View`, per ogni riga di dati dell'elenco. Crea un'istanza di `View` per ogni riga di dati che verranno visualizzati sullo schermo, con l'aspetto dell'istanza di `View` definito in un file di layout. Quando una cella scompare dallo schermo a causa dello scorrimento, la cella viene resa disponibile per il riutilizzo. Questo evita di sprecare memoria e garantisce che siano presenti istanze di `View` solo per i dati visualizzati sullo schermo e non per tutti i dati dell'elenco. Per altre informazioni sul riutilizzo della vista, vedere [Row View Re-use](~/android/user-interface/layouts/list-view/populating.md) (Riutilizzo della vista righe).

Il metodo `GetView` inoltre popola l'istanza di `View` con dati, leggendo i dati di immagine del nome file specificato nella proprietà `ImageFilename`.

Il layout di ogni cella visualizzata dal `ListView` nativo è definito nel file di layout `NativeAndroidListViewCell.axml`, che viene espanso dal metodo `LayoutInflater.Inflate`. L'esempio di codice seguente illustra la definizione del layout:

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

Questo layout specifica che due controlli `TextView` e un controllo `ImageView` vengono usati per visualizzare il contenuto della cella. I due controlli `TextView` sono orientati verticalmente all'interno di un controllo `LinearLayout` e tutti i controlli sono contenuti in un `RelativeLayout`.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risposta alla modifica di una proprietà per il controllo personalizzato

Se la proprietà `NativeListView.Items` cambia a causa dell'aggiunta o rimozione di elementi nell'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Per ottenere questo risultato, eseguire l'override del metodo `OnElementPropertyChanged`, come illustra l'esempio di codice seguente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

Il metodo crea una nuova istanza della classe `NativeAndroidListViewAdapter` che specifica i dati per il controllo nativo `ListView`, a condizione che sia stata modificata la proprietà `NativeListView.Items` associabile.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creazione del renderer personalizzato in UWP

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

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

Il `ListView` controllo nativo viene configurato purché il renderer personalizzato sia collegato a un nuovo Xamarin.Forms elemento. Questa configurazione prevede l'impostazione del modo in cui il controllo nativo `ListView` risponderà agli elementi selezionati, popolando i dati visualizzati dal controllo, definendo l'aspetto e il contenuto di ogni cella e registrando un gestore eventi per elaborare l'evento `SelectionChanged`. A sua volta, il gestore richiamerà l'evento `ItemSelected` specificato dal controllo personalizzato `NativeListView`. Viene annullata la `SelectionChanged` sottoscrizione dell'evento se l' Xamarin.Forms elemento a cui è associato il renderer viene modificato.

L'aspetto e il contenuto di ogni cella nativi `ListView` cella sono definiti da un elemento `DataTemplate` denominato `ListViewItemTemplate`. `DataTemplate` è archiviato nel dizionario risorse a livello di applicazione, come illustrato nell'esempio di codice seguente:

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

`DataTemplate` specifica i controlli usati per visualizzare il contenuto della cella, nonché il layout e l'aspetto. Due controlli `TextBlock` e un controllo `Image` vengono usati per visualizzare il contenuto della cella usando il data binding. Inoltre, viene usata un'istanza di `ConcatImageExtensionConverter` per concatenare l'estensione file `.jpg` a ogni nome di file di immagine. Ciò garantisce che il controllo `Image` sia in grado di caricare l'immagine ed eseguirne il rendering quando è impostata la relativa proprietà `Source`.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risposta alla modifica di una proprietà per il controllo personalizzato

Se la proprietà `NativeListView.Items` cambia a causa dell'aggiunta o rimozione di elementi nell'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Per ottenere questo risultato, eseguire l'override del metodo `OnElementPropertyChanged`, come illustra l'esempio di codice seguente:

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

Il metodo popola nuovamente il controllo nativo `ListView` con i dati modificati, a condizione che sia stata modificata la proprietà `NativeListView.Items` associabile.

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come creare un renderer personalizzato che incapsula gli elenchi specifici della piattaforma e i layout di cella nativi, consentendo un maggiore controllo sulle prestazioni dei controlli nativi.

## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-listview)
