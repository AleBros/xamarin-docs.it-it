---
title: Visualizzazioni native in XAML
description: Visualizzazioni native da iOS, Android e la piattaforma universale di Windows possono essere indicate direttamente dai file XAML di xamarin. Forms. Le proprietà e gestori di eventi possono essere impostati su visualizzazioni native e possono interagire con le visualizzazioni di xamarin. Forms. Questo articolo viene illustrato come utilizzare le visualizzazioni native dai file XAML di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2019
ms.openlocfilehash: 6d3954f44cd769ed02535eb260b9952e81e67c98
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247626"
---
# <a name="native-views-in-xaml"></a>Visualizzazioni native in XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_È possibile fare riferimento direttamente alle viste native di iOS, Android e piattaforma UWP (Universal Windows Platform) dai file XAML Novell. Forms. Le proprietà e i gestori di eventi possono essere impostati sulle visualizzazioni native e possono interagire con le visualizzazioni Novell. Forms. Questo articolo illustra come usare le visualizzazioni native dei file XAML Novell. Forms._

Questo articolo tratta gli argomenti seguenti:

- [Utilizzo di visualizzazioni native](#consuming) : processo per l'utilizzo di una visualizzazione nativa da XAML.
- [Utilizzo di associazioni native](#native_bindings) : data binding a e da proprietà di viste native.
- [Passaggio di argomenti a visualizzazioni native](#passing_arguments) : passaggio di argomenti a costruttori di visualizzazione nativi e chiamata di metodi factory di visualizzazione nativi.
- [Riferimento a visualizzazioni native dal codice](#native_view_code) : recupero di istanze di visualizzazione native dichiarate in un file XAML dal relativo file code-behind.
- [Sottoclassi delle visualizzazioni native](#subclassing) : sottoclassi delle visualizzazioni native per definire un'API XAML.  

<a name="overview" />

## <a name="overview"></a>Panoramica

Per incorporare una visualizzazione nativa in un file XAML di xamarin. Forms:

1. Aggiungere una dichiarazione dello spazio dei nomi `xmlns` nel file XAML per lo spazio dei nomi che contiene la visualizzazione nativa.
1. Creare un'istanza della visualizzazione nativa nel file XAML.

> [!IMPORTANT]
> Il codice XAML compilato deve essere disabilitato per tutte le pagine XAML che usano viste native. Questa operazione può essere eseguita decorando la classe code-behind per la pagina XAML con l'attributo `[XamlCompilation(XamlCompilationOptions.Skip)]`. Per altre informazioni sulla compilazione XAML, vedere [compilazione XAML in Novell. Forms](~/xamarin-forms/xaml/xamlc.md).

Per fare riferimento a una visualizzazione nativa da un file code-behind, è necessario usare un progetto di Asset condivisi (SAP) e inserire il codice specifico della piattaforma con direttive di compilazione condizionale. Per ulteriori informazioni, vedere [riferimento a visualizzazioni native dal codice](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Utilizzo di visualizzazioni Native

Nell'esempio di codice seguente viene illustrato l'utilizzo di viste native per ogni piattaforma in un [`ContentPage`](xref:Xamarin.Forms.ContentPage)Novell. Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Oltre a specificare il `clr-namespace` e `assembly` per uno spazio dei nomi di visualizzazione nativo, è necessario specificare anche un `targetPlatform`. Deve essere impostato su `iOS`, `Android`, `UWP``Windows` (equivalente a `UWP`), `macOS`, `GTK`, `Tizen`o `WPF`. In fase di esecuzione, il parser XAML ignorerà tutti i prefissi degli spazi dei nomi XML con `targetPlatform` che non corrispondono alla piattaforma in cui è in esecuzione l'applicazione.

Ogni dichiarazione dello spazio dei nomi è utilizzabile per fare riferimento a qualsiasi classe o struttura dallo spazio dei nomi specificato. Ad esempio, è possibile usare la dichiarazione dello spazio dei nomi `ios` per fare riferimento a qualsiasi classe o struttura dallo spazio dei nomi di `UIKit` iOS. Proprietà della vista nativa può essere impostata tramite XAML, ma i tipi di oggetto e proprietà devono corrispondere. Ad esempio, la proprietà `UILabel.TextColor` è impostata su `UIColor.Red` utilizzando l'estensione di markup `x:Static` e lo spazio dei nomi `ios`.

Le proprietà associabili e le proprietà associabili associate possono essere impostate anche nelle viste native usando la sintassi `Class.BindableProperty="value"`. Ogni visualizzazione nativa viene racchiusa in un'istanza di `NativeViewWrapper` specifica della piattaforma che deriva dalla classe [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) . L'impostazione di una proprietà associabile o una proprietà associabile associata in una vista native trasferisce il valore della proprietà per il wrapper. È possibile, ad esempio, specificare un layout orizzontale centrato impostando `View.HorizontalOptions="Center"` nella visualizzazione nativa.

> [!NOTE]
> Si noti che gli stili non possono essere usati con le visualizzazioni native, perché gli stili possono solo proprietà di destinazione supportate da `BindableProperty` oggetti.

I costruttori di widget Android richiedono in genere l'oggetto `Context` Android come argomento e possono essere resi disponibili tramite una proprietà statica nella classe `MainActivity`. Pertanto, quando si crea un widget Android in XAML, l'oggetto `Context` deve essere in genere passato al costruttore del widget usando l'attributo `x:Arguments` con un'estensione di markup `x:Static`. Per ulteriori informazioni, vedere [passaggio di argomenti a visualizzazioni native](#passing_arguments).

> [!NOTE]
> Si noti che la denominazione di una vista nativa con `x:Name` non è possibile in un progetto di libreria .NET Standard o in un progetto di asset condiviso (SAP). In questo modo genererà una variabile del tipo nativo, che provoca un errore di compilazione. Tuttavia, le visualizzazioni native possono essere incapsulate in `ContentView` istanze e recuperate nel file code-behind, purché venga utilizzato un SAP. Per ulteriori informazioni, vedere [riferimento a una visualizzazione nativa dal codice](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Binding nativo

Data binding utilizzato per sincronizzare un'interfaccia utente con l'origine dati e semplifica le operazioni come un'applicazione xamarin. Forms Visualizza e interagisce con i relativi dati. A condizione che l'oggetto di origine implementi l'interfaccia `INotifyPropertyChanged`, le modifiche all'oggetto di *origine* vengono automaticamente inserite nell'oggetto di *destinazione* dal framework di associazione e, facoltativamente, è possibile eseguire il push delle modifiche nell'oggetto di *destinazione* all'oggetto di *origine* .

Proprietà di visualizzazioni native è anche possibile usare l'associazione dati. Esempio di codice seguente viene illustrata l'associazione di dati usando proprietà dell'oggetto visualizzazioni native:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

La pagina contiene un [`Entry`](xref:Xamarin.Forms.Entry) la cui proprietà [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) è associata alla proprietà `NativeSwitchPageViewModel.IsSwitchOn`. Il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostato su una nuova istanza della classe `NativeSwitchPageViewModel` nel file code-behind, con la classe ViewModel che implementa l'interfaccia `INotifyPropertyChanged`.

Questa pagina contiene anche un commutatore nativo per ogni piattaforma. Ogni opzione nativa utilizza un'associazione [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) per aggiornare il valore della proprietà `NativeSwitchPageViewModel.IsSwitchOn`. Pertanto, quando l'opzione è disattivata, il `Entry` è disabilitato e quando l'opzione è attivata, il `Entry` è abilitato. Le schermate seguenti illustrano questa funzionalità in ogni piattaforma:

![](xaml-images/native-switch-disabled.png "Native Switch Disabled")
![](xaml-images/native-switch-enabled.png "Native Switch Enabled")

Le associazioni bidirezionali sono supportate automaticamente purché la proprietà nativa implementi `INotifyPropertyChanged`o supporti l'osservazione chiave-valore (KVO) in iOS o sia una `DependencyProperty` in UWP. Tuttavia, molte visualizzazioni native non supportano la notifica di modifiche. Per queste visualizzazioni, è possibile specificare un [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valore della proprietà come parte dell'espressione di associazione. Questa proprietà deve essere impostata sul nome di un evento nella visualizzazione nativa che segnala quando viene modificata la proprietà di destinazione. Quindi, quando cambia il valore del commutatore nativo, la classe `Binding` riceve una notifica che indica che l'utente ha modificato il valore dell'opzione e il valore della proprietà `NativeSwitchPageViewModel.IsSwitchOn` è stato aggiornato.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passaggio di argomenti per le visualizzazioni Native

Gli argomenti del costruttore possono essere passati alle viste native usando l'attributo `x:Arguments` con un'estensione di markup `x:Static`. Inoltre, i metodi factory di visualizzazione nativi (`public static` metodi che restituiscono oggetti o valori dello stesso tipo della classe o della struttura che definisce i metodi) possono essere chiamati specificando il nome del metodo usando l'attributo `x:FactoryMethod` e i relativi argomenti usando l'attributo `x:Arguments`.

L'esempio di codice seguente illustra entrambe le tecniche:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

Il metodo factory [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) viene usato per impostare la proprietà [`UILabel.Font`](xref:UIKit.UILabel.Font) su un nuovo [`UIFont`](xref:UIKit.UIFont) in iOS. Il nome e le dimensioni del `UIFont` vengono specificati dagli argomenti del metodo figlio dell'attributo `x:Arguments`.

Il metodo factory [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) viene usato per impostare la proprietà [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) su un nuovo [`Typeface`](xref:Android.Graphics.Typeface) in Android. Il nome e lo stile della famiglia `Typeface` sono specificati dagli argomenti del metodo figlio dell'attributo `x:Arguments`.

Il costruttore [`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) viene utilizzato per impostare la proprietà [`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) su un nuovo `FontFamily` nel piattaforma UWP (Universal Windows Platform) (UWP). Il nome del `FontFamily` viene specificato dall'argomento del metodo che è figlio dell'attributo `x:Arguments`.

> [!NOTE]
> Gli argomenti devono corrispondere ai tipi necessari per il costruttore o al metodo factory.

Gli screenshot seguenti mostrano il risultato della specifica gli argomenti di metodi e costruttori factory per impostare il tipo di carattere per le visualizzazioni native diversi:

![](xaml-images/passing-arguments.png "Setting Fonts on Native Views")

Per ulteriori informazioni sul passaggio di argomenti in XAML, vedere [passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Riferimento per le visualizzazioni Native da codice

Sebbene non sia possibile assegnare un nome a una visualizzazione nativa con l'attributo `x:Name`, è possibile recuperare un'istanza di visualizzazione nativa dichiarata in un file XAML dal relativo file code-behind in un progetto di accesso condiviso, purché la visualizzazione nativa sia un elemento figlio di un [`ContentView`](xref:Xamarin.Forms.ContentView) che specifichi un valore di attributo `x:Name`. Quindi, all'interno di direttive di compilazione condizionale nel file code-behind è necessario:

1. Recuperare il valore della proprietà [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) e eseguirne il cast a un tipo di `NativeViewWrapper` specifico della piattaforma.
1. Recuperare la proprietà `NativeViewWrapper.NativeElement` e eseguirne il cast al tipo di visualizzazione nativo.

L'API nativa può quindi essere richiamato sulle visualizzazione nativa per eseguire le operazioni desiderate. Questo approccio offre anche il vantaggio che più visualizzazioni native XAML per diverse piattaforme possono essere elementi figlio della stessa [`ContentView`](xref:Xamarin.Forms.ContentView). L'esempio di codice seguente illustra questa tecnica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

Nell'esempio precedente, le viste native per ogni piattaforma sono elementi figlio di [`ContentView`](xref:Xamarin.Forms.ContentView) controlli, con il valore dell'attributo `x:Name` usato per recuperare il `ContentView` nel code-behind:

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

È possibile accedere alla proprietà [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) per recuperare la visualizzazione nativa di cui è stato eseguito il wrapped come istanza di `NativeViewWrapper` specifica della piattaforma. Viene quindi eseguito l'accesso alla proprietà `NativeViewWrapper.NativeElement` per recuperare la visualizzazione nativa come tipo nativo. API nativa della visualizzazione viene quindi richiamato per eseguire le operazioni desiderate.

I pulsanti nativi iOS e Android condividono lo stesso `OnButtonTap` gestore eventi, perché ogni pulsante nativo utilizza un delegato di `EventHandler` in risposta a un evento Touch. Tuttavia, il piattaforma UWP (Universal Windows Platform) (UWP) utilizza un `RoutedEventHandler`separato, che a sua volta utilizza il gestore dell'evento `OnButtonTap` in questo esempio. Pertanto, quando si fa clic su un pulsante nativo, viene eseguito il gestore dell'evento `OnButtonTap`, che consente di ridimensionare e ruotare il controllo nativo contenuto all'interno della [`ContentView`](xref:Xamarin.Forms.ContentView) denominata `contentViewTextParent`. Gli screenshot seguenti illustrano questo corso in ogni piattaforma:

![](xaml-images/contentview.png "ContentView Containing a Native Control")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Creazione di una sottoclasse le visualizzazioni Native

Molti iOS e Android native visualizzazioni non sono adatte per creare un'istanza in XAML, perché usano i metodi, anziché le proprietà, per impostare il controllo. Per risolvere questo problema è per le visualizzazioni native sottoclasse nei wrapper che definiscono un'API più facile integrazione con XAML che usa le proprietà per configurare il controllo e che utilizza eventi indipendenti dalla piattaforma. Le visualizzazioni native sottoposta a wrapping possono essere inserite in un condiviso Asset Project (SAP) e racchiuso direttive di compilazione condizionale, o inserire nei progetti specifici della piattaforma e fare riferimento da XAML in un progetto di libreria .NET Standard.

Esempio di codice seguente illustra una pagina di xamarin. Forms che utilizza una sottoclasse le visualizzazioni native:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

La pagina contiene un [`Label`](xref:Xamarin.Forms.Label) che Visualizza il frutto scelto dall'utente da un controllo nativo. Il `Label` viene associato alla proprietà `SubclassedNativeControlsPageViewModel.SelectedFruit`. Il [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostato su una nuova istanza della classe `SubclassedNativeControlsPageViewModel` nel file code-behind, con la classe ViewModel che implementa l'interfaccia `INotifyPropertyChanged`.

La pagina contiene anche una visualizzazione di selezione nativo per ogni piattaforma. Ogni visualizzazione nativa Visualizza la raccolta di frutti associando la relativa proprietà `ItemSource` alla raccolta di `SubclassedNativeControlsPageViewModel.Fruits`. In questo modo l'utente può selezionare un fruit, come illustrato negli screenshot seguenti:

![](xaml-images/sub-classed.png "Subclassed Native Views")

In iOS e Android i controlli di selezione native usare metodi per configurare i controlli. Di conseguenza, questi controlli di selezione deve essere sottoclassati per esporre le proprietà per fare in modo facile integrazione con XAML. Nel piattaforma UWP (Universal Windows Platform) (UWP), il `ComboBox` è già descrittivo per XAML e pertanto non richiede la sottoclasse.

### <a name="ios"></a>iOS

L'implementazione di iOS sottoclassi la visualizzazione [`UIPickerView`](xref:UIKit.UIPickerView) ed espone le proprietà e un evento che può essere facilmente utilizzato da XAML:

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

La classe `MyUIPickerView` espone le proprietà `ItemsSource` e `SelectedItem` e un evento `SelectedItemChanged`. Un [`UIPickerView`](xref:UIKit.UIPickerView) richiede un modello di dati [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) sottostante, a cui è possibile accedere tramite le proprietà `MyUIPickerView` e l'evento. Il modello di dati `UIPickerViewModel` viene fornito dalla classe `PickerModel`:

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

La classe `PickerModel` fornisce l'archiviazione sottostante per la classe `MyUIPickerView`, tramite la proprietà `Items`. Ogni volta che l'elemento selezionato nel `MyUIPickerView` viene modificato, viene eseguito il metodo [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) , che aggiorna l'indice selezionato e genera l'evento di `ItemChanged`. Ciò garantisce che la proprietà `SelectedItem` restituisca sempre l'ultimo elemento selezionato dall'utente. Inoltre, la classe `PickerModel` esegue l'override dei metodi utilizzati per configurare l'istanza di `MyUIPickerView`.

### <a name="android"></a>Android

L'implementazione di Android sottoclasses the [`Spinner`](xref:Android.Widget.Spinner) View ed espone le proprietà e un evento che può essere facilmente utilizzato da XAML:

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

La classe `MySpinner` espone le proprietà `ItemsSource` e `SelectedObject` e un evento `ItemSelected`. Gli elementi visualizzati dalla classe `MySpinner` vengono forniti dall' [`Adapter`](xref:Android.Widget.Adapter) associato alla visualizzazione e gli elementi vengono popolati nel `Adapter` quando la proprietà `ItemsSource` è impostata per la prima volta. Ogni volta che l'elemento selezionato nella classe `MySpinner` cambia, il gestore dell'evento `OnBindableSpinnerItemSelected` aggiorna la proprietà `SelectedObject`.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare le visualizzazioni native dai file XAML di xamarin. Forms. Le proprietà e gestori di eventi possono essere impostati su visualizzazioni native e possono interagire con le visualizzazioni di xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [NativeSwitch (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Form nativi](~/xamarin-forms/platform/native-forms.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
