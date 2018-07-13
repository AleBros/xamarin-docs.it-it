---
title: Personalizzazione di un ListView
description: Un ListView di xamarin. Forms è una vista contenente una raccolta di dati sotto forma di elenco verticale. Questo articolo illustra come creare un renderer personalizzato che incapsula i controlli elenco di specifiche della piattaforma e i layout delle celle native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo.
ms.prod: xamarin
ms.assetid: 2FBCB8C8-4F32-45E7-954F-63AD29D5F1B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: b3b73d542faebdb8ab85c989d7812368f4f3ffac
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997487"
---
# <a name="customizing-a-listview"></a>Personalizzazione di un ListView

_Un ListView di xamarin. Forms è una vista contenente una raccolta di dati sotto forma di elenco verticale. Questo articolo illustra come creare un renderer personalizzato che incapsula i controlli elenco di specifiche della piattaforma e i layout delle celle native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo._

Tutte le visualizzazioni di xamarin. Forms sono un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `ListView` ](xref:Xamarin.Forms.ListView) viene eseguito il rendering da un'applicazione xamarin. Forms, in iOS il `ListViewRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `UITableView` controllo. La piattaforma Android, il `ListViewRenderer` un'istanza nativa `ListView` controllo. In Universal Windows Platform (UWP), il `ListViewRenderer` un'istanza nativa `ListView` controllo. Per altre informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `ListView` ](xref:Xamarin.Forms.ListView) controllo e i corrispondenti controlli nativi che l'implementano:

![](listview-images/listview-classes.png "Relazione tra i controlli nativi di implementazione e il controllo ListView")

Il processo di rendering possibile avvantaggiarsi di implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per un [ `ListView` ](xref:Xamarin.Forms.ListView) in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_ListView_Control) un controllo personalizzato di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento a questo punto discuteremo a sua volta, per implementare un `NativeListView` renderer che si avvale di controlli di elenco di specifiche della piattaforma e i layout di cella nativo. Questo scenario è utile durante il trasferimento di un'app nativa esistente che contiene l'elenco e il codice di cella che può essere usato nuovamente. Inoltre, consente la personalizzazione dettagliata delle funzionalità di controllo elenco che può influire sulle prestazioni, ad esempio la virtualizzazione dei dati.

<a name="Creating_the_Custom_ListView_Control" />

## <a name="creating-the-custom-listview-control"></a>Creazione di un controllo ListView personalizzato

Una classe personalizzata [ `ListView` ](xref:Xamarin.Forms.ListView) controllo può essere creato dalla creazione di una sottoclasse di `ListView` classe, come illustrato nell'esempio di codice seguente:

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

Il `NativeListView` viene creato nel progetto della libreria .NET Standard e definisce l'API per il controllo personalizzato. Questo controllo espone un `Items` proprietà usata per popolare il `ListView` con i dati e i dati associati a ai fini della visualizzazione. Espone inoltre un `ItemSelected` evento che verrà generato ogni volta che viene selezionato un elemento in un controllo elenco native specifiche della piattaforma. Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Utilizzo del controllo personalizzato

Il `NativeListView` controllo personalizzato può fare riferimento nel codice Xaml nel progetto della libreria .NET Standard dichiara uno spazio dei nomi per la relativa posizione e usando il prefisso dello spazio dei nomi del controllo. Nell'esempio di codice riportato di seguito viene illustrato come il `NativeListView` controlli personalizzati possono essere usati da una pagina XAML:

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

Il `local` prefisso dello spazio dei nomi può essere denominata alcuna operazione. Tuttavia, il `clr-namespace` e `assembly` valori devono corrispondere i dettagli del controllo personalizzato. Una volta lo spazio dei nomi è dichiarato, il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il `NativeListView` controlli personalizzati possono essere usati da una pagina di c#:

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

Il `NativeListView` controllo personalizzato Usa renderer personalizzati specifici della piattaforma per visualizzare un elenco di dati, che viene compilati tramite il `Items` proprietà. Ogni riga nell'elenco contiene tre elementi di dati, un nome, una categoria e un nome di file di immagine. Il layout di ogni riga nell'elenco è definito dal renderer personalizzati specifici della piattaforma.

> [!NOTE]
> Poiché il `NativeListView` controllo personalizzato eseguito usando i controlli di elenco di specifiche della piattaforma che includono lo scorrimento capacità, il controllo personalizzato deve non essere ospitato nei controlli di layout scorrevole, ad esempio il [ `ScrollView` ](xref:Xamarin.Forms.ScrollView).

Un renderer personalizzato può ora essere aggiunti a ogni progetto di applicazione per creare i controlli elenco di specifiche della piattaforma e i layout di cella nativo.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creare il Renderer personalizzato a seconda della piattaforma

Il processo per la creazione della classe renderer personalizzati è come segue:

1. Creare una sottoclasse del `ListViewRenderer` classe che esegue il rendering del controllo personalizzato.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering il controllo e scrivere la logica personalizzata per personalizzarla. Questo metodo viene chiamato quando il corrispondente xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) viene creato.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per eseguire il rendering del controllo personalizzato di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> È facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, quindi verrà utilizzato il renderer predefinito per la classe di base della cella.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](listview-images/solution-structure.png "Responsabilità di progetto Renderer personalizzati NativeListView")

Il `NativeListView` viene eseguito il rendering di controlli personalizzati dalle classi renderer specifica della piattaforma, che derivano da tutti i `ListViewRenderer` classe per ogni piattaforma. Di conseguenza ogni `NativeListView` controllo personalizzato viene eseguito il rendering con controlli elenco di specifiche della piattaforma e i layout delle celle nativi, come illustrato negli screenshot seguenti:

![](listview-images/screenshots.png "NativeListView in ogni piattaforma")

Il `ListViewRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo personalizzato di xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro, che contiene `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* collegati rispettivamente. Nell'applicazione di esempio, il `OldElement` proprietà sarà `null` e il `NewElement` proprietà conterrà un riferimento al `NativeListView` istanza.

Una versione override del `OnElementChanged` metodo, in ogni classe renderer specifica della piattaforma, è possibile eseguire la personalizzazione del controllo nativo. Un riferimento tipizzato al controllo nativo utilizzato nella piattaforma sono accessibili tramite il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà.

Prestare attenzione quando si sottoscrive i gestori di eventi nel `OnElementChanged` metodo, come illustrato nell'esempio di codice seguente:

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

Deve essere configurato solo del controllo nativo e sottoscrivere i gestori degli eventi quando il renderer personalizzato è associato a un nuovo elemento di xamarin. Forms. Analogamente, i gestori degli eventi sottoscritti deve essere annullate solo quando l'elemento del renderer è associato alle modifiche. Adottando questo approccio consente di creare un renderer personalizzato che non subisce perdite di memoria.

Una versione override del `OnElementPropertyChanged` metodo, in ogni classe renderer specifica della piattaforma, è possibile rispondere alle modifiche di proprietà associabili del controllo personalizzato di xamarin. Forms. Un controllo per la proprietà viene modificata deve essere sempre eseguito, come la sostituzione può essere chiamata più volte.

Ogni classe renderer personalizzato è decorata con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo personalizzato xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni classe renderer personalizzato specifico della piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

Il `UITableView` controllo viene configurato tramite la creazione di un'istanza di `NativeiOSListViewSource` classe, a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms. Questa classe fornisce i dati per il `UITableView` controllo eseguendo l'override di `RowsInSection` e `GetCell` metodi dal `UITableViewSource` classe e l'esposizione di un `Items` proprietà che contiene l'elenco di dati da visualizzare. La classe fornisce anche un `RowSelected` override del metodo che richiama la `ItemSelected` evento fornito dal `NativeListView` controllo personalizzato. Per altre informazioni sul metodo di override, vedere [creazione di una sottoclasse UITableViewSource](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). Il `GetCell` metodo restituisce un `UITableCellView` che viene popolato con i dati per ogni riga nell'elenco e viene illustrato nell'esempio di codice seguente:

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

Questo metodo crea un `NativeiOSListViewCell` istanza per ogni riga di dati che verranno visualizzati sullo schermo. Il `NativeiOSCell` istanza definisce il layout di ogni cella e i dati della cella. Quando una cella viene rimossa dallo schermo a causa di scorrimento, la cella saranno disponibile per il riutilizzo. Questo modo si evita di sprecare memoria assicurando che sono presenti solo `NativeiOSCell` istanze per i dati da visualizzare su schermo, anziché tutti i dati nell'elenco. Per altre informazioni sul riutilizzo di cella, vedere [cella riutilizzare](~/ios/user-interface/controls/tables/populating-a-table-with-data.md). Il `GetCell` metodo legge anche il `ImageFilename` proprietà di ogni riga di dati, specificati che è presente e legge l'immagine e lo archivia come una `UIImage` istanza, prima di aggiornare il `NativeiOSListViewCell` dell'istanza con i dati (nome, categoria e immagine) la riga.

Il `NativeiOSListViewCell` classe definisce il layout per ogni cella e viene illustrato nell'esempio di codice seguente:

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

Questa classe definisce i controlli usati per eseguire il rendering, il contenuto della cella e il relativo layout. Il `NativeiOSListViewCell` costruttore crea istanze di `UILabel` e `UIImageView` controlla e inizializza l'aspetto. Questi controlli vengono usati per visualizzare i dati di ogni riga, con la `UpdateCell` il metodo utilizzato per impostare questi dati nel `UILabel` e `UIImageView` istanze. Il percorso di queste istanze viene impostato dal sottoposto a override `LayoutSubviews` metodo, specificando le coordinate all'interno della cella.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risponde a una modifica della proprietà sul controllo personalizzato

Se il `NativeListView.Items` proprietà cambia a causa di elementi da aggiungere a o rimosso dall'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Ciò può essere effettuata eseguendo l'override di `OnElementPropertyChanged` metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Source = new NativeiOSListViewSource (Element as NativeListView);
  }
}
```

Il metodo crea una nuova istanza della `NativeiOSListViewSource` classe che fornisce dati per il `UITableView` controllo, a condizione che l'associabile `NativeListView.Items` proprietà è stata modificata.

### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

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

L'oggetto nativo `ListView` controllo viene configurato a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms. Questa configurazione prevede la creazione di un'istanza del `NativeAndroidListViewAdapter` classe che fornisce dati all'oggetto nativo `ListView` controllare e registrare un gestore eventi per elaborare il `ItemClick` evento. A sua volta, verrà richiamato il gestore di `ItemSelected` eventi forniti dal `NativeListView` controllo personalizzato. Il `ItemClick` evento viene annullato la sottoscrizione a, se l'elemento di xamarin. Forms il renderer è associato alle modifiche.

Il `NativeAndroidListViewAdapter` deriva dal `BaseAdapter` classe ed espone un `Items` proprietà che contiene l'elenco di dati da visualizzare, nonché eseguire l'override di `Count`, `GetView`, `GetItemId`, e `this[int]` metodi. Per altre informazioni su questi override dei metodi, vedere [che implementa un ListAdapter](~/android/user-interface/layouts/list-view/populating.md). Il `GetView` metodo restituisce una visualizzazione per ogni riga, popolato con i dati e viene illustrato nell'esempio di codice seguente:

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

Il `GetView` metodo viene chiamato per restituire la cella da sottoporre a rendering, come un `View`, per ogni riga di dati nell'elenco. Crea un `View` per ogni riga di dati che verranno visualizzati sullo schermo, con l'aspetto dell'istanza di `View` istanza definita in un file di layout. Quando una cella viene rimossa dallo schermo a causa di scorrimento, la cella saranno disponibile per il riutilizzo. Questo modo si evita di sprecare memoria assicurando che sono presenti solo `View` istanze per i dati da visualizzare su schermo, anziché tutti i dati nell'elenco. Per altre informazioni sul riutilizzo di visualizzazione, vedere [riutilizzo delle visualizzazioni riga](~/android/user-interface/layouts/list-view/populating.md).

Il `GetView` metodo inoltre compila il `View` con dati, tra cui leggere i dati dell'immagine dal nome del file specificato nell'istanza di `ImageFilename` proprietà.

Il layout di ogni cella dispayed da nativi `ListView` definito nel `NativeAndroidListViewCell.axml` file di layout, che viene ingrandita in base il `LayoutInflater.Inflate` (metodo). Esempio di codice seguente illustra la definizione del layout:

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

Questo layout specifica che due `TextView` controlli e un `ImageView` controllo consentono di visualizzare il contenuto della cella. I due `TextView` controlli sono orientati verticalmente all'interno di un `LinearLayout` controllo, con tutti i controlli sia indipendente all'interno di un `RelativeLayout`.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risponde a una modifica della proprietà sul controllo personalizzato

Se il `NativeListView.Items` proprietà cambia a causa di elementi da aggiungere a o rimosso dall'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Ciò può essere effettuata eseguendo l'override di `OnElementPropertyChanged` metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
protected override void OnElementPropertyChanged (object sender, System.ComponentModel.PropertyChangedEventArgs e)
{
  base.OnElementPropertyChanged (sender, e);

  if (e.PropertyName == NativeListView.ItemsProperty.PropertyName) {
    Control.Adapter = new NativeAndroidListViewAdapter (_context as Android.App.Activity, Element as NativeListView);
  }
}
```

Il metodo crea una nuova istanza della `NativeAndroidListViewAdapter` classe che fornisce dati all'oggetto nativo `ListView` controllo, a condizione che l'associabile `NativeListView.Items` proprietà è stata modificata.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creare il Renderer personalizzato nella piattaforma UWP

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

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

L'oggetto nativo `ListView` controllo viene configurato a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms. Questa configurazione prevede l'impostazione modalità nativa `ListView` controllo risponderà agli elementi selezionati in corso, popolamento di dati visualizzato dal controllo, che definisce l'aspetto e il contenuto di ogni cella e la registrazione di un gestore eventi per elaborare le `SelectionChanged` evento. A sua volta, verrà richiamato il gestore di `ItemSelected` eventi forniti dal `NativeListView` controllo personalizzato. Il `SelectionChanged` evento viene annullato la sottoscrizione a, se l'elemento di xamarin. Forms il renderer è associato alle modifiche.

L'aspetto e il contenuto di ogni nativi `ListView` cella sono definiti da un `DataTemplate` denominata `ListViewItemTemplate`. Ciò `DataTemplate` vengono archiviati nel dizionario risorse a livello di applicazione e viene illustrato nell'esempio di codice seguente:

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

Il `DataTemplate` specifica i controlli usati per visualizzare il contenuto della cella e il layout e l'aspetto. Due `TextBlock` controlli e un `Image` controllo consentono di visualizzare il contenuto della cella tramite associazione dati. Inoltre, un'istanza del `ConcatImageExtensionConverter` consente di concatenare il `.jpg` estensione file da ogni nome di file di immagine. Ciò garantisce che il `Image` controllo è possibile caricare ed eseguire il rendering dell'immagine quando è `Source` è impostata.

#### <a name="responding-to-a-property-change-on-the-custom-control"></a>Risponde a una modifica della proprietà sul controllo personalizzato

Se il `NativeListView.Items` proprietà cambia a causa di elementi da aggiungere a o rimosso dall'elenco, il renderer personalizzato deve rispondere visualizzando le modifiche. Ciò può essere effettuata eseguendo l'override di `OnElementPropertyChanged` metodo, che viene visualizzato nell'esempio di codice seguente:

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

Il metodo nuovamente popola l'oggetto nativo `ListView` controllo con i dati modificati, a condizione che l'associabile `NativeListView.Items` proprietà è stata modificata.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer personalizzato che incapsula i controlli elenco di specifiche della piattaforma e i layout delle celle native, consentendo maggiore controllo sulle prestazioni del controllo elenco nativo.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererListView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/listview/)
