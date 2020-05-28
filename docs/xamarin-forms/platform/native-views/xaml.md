---
title: ''
description: È possibile fare riferimento direttamente alle viste native di iOS, Android e piattaforma UWP (Universal Windows Platform) da Xamarin.Forms file XAML. Le proprietà e i gestori di eventi possono essere impostati sulle visualizzazioni native e possono interagire con le Xamarin.Forms visualizzazioni. In questo articolo viene illustrato come utilizzare viste native da Xamarin.Forms file XAML.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 67994371d3042100503eb3a7c3bc7d117b1c590c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139581"
---
# <a name="native-views-in-xaml"></a>Visualizzazioni native in XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_È possibile fare riferimento direttamente alle viste native di iOS, Android e piattaforma UWP (Universal Windows Platform) da Xamarin.Forms file XAML. Le proprietà e i gestori di eventi possono essere impostati sulle visualizzazioni native e possono interagire con le Xamarin.Forms visualizzazioni. In questo articolo viene illustrato come utilizzare viste native da Xamarin.Forms file XAML._

Questo articolo tratta gli argomenti seguenti:

- [Utilizzo di visualizzazioni native](#consuming) : processo per l'utilizzo di una visualizzazione nativa da XAML.
- [Utilizzo di associazioni native](#native_bindings) : data binding a e da proprietà di viste native.
- [Passaggio di argomenti a visualizzazioni native](#passing_arguments) : passaggio di argomenti a costruttori di visualizzazione nativi e chiamata di metodi factory di visualizzazione nativi.
- [Riferimento a visualizzazioni native dal codice](#native_view_code) : recupero di istanze di visualizzazione native dichiarate in un file XAML dal relativo file code-behind.
- [Sottoclassi delle visualizzazioni native](#subclassing) : sottoclassi delle visualizzazioni native per definire un'API XAML.  

<a name="overview" />

## <a name="overview"></a>Panoramica

Per incorporare una visualizzazione nativa in un Xamarin.Forms file XAML:

1. Aggiungere una `xmlns` dichiarazione dello spazio dei nomi nel file XAML per lo spazio dei nomi che contiene la visualizzazione nativa.
1. Creare un'istanza della visualizzazione nativa nel file XAML.

> [!IMPORTANT]
> Il codice XAML compilato deve essere disabilitato per tutte le pagine XAML che usano viste native. Questa operazione può essere eseguita decorando la classe code-behind per la pagina XAML con l' `[XamlCompilation(XamlCompilationOptions.Skip)]` attributo. Per ulteriori informazioni sulla compilazione XAML, vedere [compilazione XAML in Xamarin.Forms ](~/xamarin-forms/xaml/xamlc.md).

Per fare riferimento a una visualizzazione nativa da un file code-behind, è necessario usare un progetto asset condiviso (SAP) ed eseguire il wrapping del codice specifico della piattaforma con le direttive di compilazione condizionale. Per ulteriori informazioni, vedere [riferimento a visualizzazioni native dal codice](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Utilizzo di visualizzazioni native

Nell'esempio di codice seguente viene illustrato l'utilizzo di viste native per ogni piattaforma in un Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

Oltre a specificare `clr-namespace` e `assembly` per uno spazio dei nomi di visualizzazione nativo, `targetPlatform` è necessario specificare anche un. Deve essere impostato su `iOS` ,, `Android` `UWP` , `Windows` (equivalente a),,, `UWP` `macOS` `GTK` `Tizen` o `WPF` . In fase di esecuzione, il parser XAML ignorerà tutti i prefissi degli spazi dei nomi XML che hanno un `targetPlatform` che non corrisponde alla piattaforma in cui è in esecuzione l'applicazione.

Ogni dichiarazione dello spazio dei nomi può essere utilizzata per fare riferimento a qualsiasi classe o struttura dallo spazio dei nomi specificato. Ad esempio, la `ios` dichiarazione dello spazio dei nomi può essere usata per fare riferimento a qualsiasi classe o struttura dallo `UIKit` spazio dei nomi iOS. Le proprietà della visualizzazione nativa possono essere impostate tramite XAML, ma i tipi di oggetto e proprietà devono corrispondere. Ad esempio, la `UILabel.TextColor` proprietà viene impostata su `UIColor.Red` usando l' `x:Static` estensione di markup e lo `ios` spazio dei nomi.

Le proprietà associabili e le proprietà associabili associate possono essere impostate anche nelle viste native usando la `Class.BindableProperty="value"` sintassi. Ogni visualizzazione nativa viene racchiusa in un'istanza specifica della piattaforma `NativeViewWrapper` , che deriva dalla [`Xamarin.Forms.View`](xref:Xamarin.Forms.View) classe. Impostando una proprietà associabile o una proprietà associabile associata in una visualizzazione nativa, il valore della proprietà viene trasferito al wrapper. È ad esempio possibile specificare un layout orizzontale centrato impostando `View.HorizontalOptions="Center"` sulla visualizzazione nativa.

> [!NOTE]
> Si noti che gli stili non possono essere usati con le visualizzazioni native, perché gli stili possono solo proprietà di destinazione supportate da `BindableProperty` oggetti.

I costruttori di widget Android richiedono in genere l' `Context` oggetto Android come argomento e possono essere resi disponibili tramite una proprietà statica nella `MainActivity` classe. Pertanto, quando si crea un widget Android in XAML, l' `Context` oggetto deve essere in genere passato al costruttore del widget usando l' `x:Arguments` attributo con un' `x:Static` estensione di markup. Per ulteriori informazioni, vedere [passaggio di argomenti a visualizzazioni native](#passing_arguments).

> [!NOTE]
> Si noti che la denominazione di una vista nativa con `x:Name` non è possibile in un progetto di libreria .NET standard o in un progetto di asset condiviso (SAP). In questo modo verrà generata una variabile del tipo nativo, che causerà un errore di compilazione. Tuttavia, le visualizzazioni native possono essere incapsulate in `ContentView` istanze e recuperate nel file code-behind, purché venga utilizzato un SAP. Per ulteriori informazioni, vedere [riferimento a una visualizzazione nativa dal codice](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Associazioni Native

Il data binding viene usato per sincronizzare un'interfaccia utente con la relativa origine dati e semplifica il modo in cui un' Xamarin.Forms applicazione Visualizza e interagisce con i relativi dati. A condizione che l'oggetto di origine implementi l' `INotifyPropertyChanged` interfaccia, le modifiche all'oggetto di *origine* vengono automaticamente inserite nell'oggetto di *destinazione* dal framework di associazione e, facoltativamente, è possibile effettuare il push delle modifiche nell'oggetto di *destinazione* all'oggetto di *origine* .

Anche le proprietà delle visualizzazioni native possono usare data binding. Nell'esempio di codice riportato di seguito viene illustrato data binding utilizzando le proprietà delle visualizzazioni native:

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

La pagina contiene un oggetto la [`Entry`](xref:Xamarin.Forms.Entry) cui [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà è associata alla `NativeSwitchPageViewModel.IsSwitchOn` Proprietà. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostata su una nuova istanza della `NativeSwitchPageViewModel` classe nel file code-behind, con la classe ViewModel che implementa l' `INotifyPropertyChanged` interfaccia.

La pagina contiene anche un Commuti nativo per ogni piattaforma. Ogni opzione nativa utilizza un' [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) associazione per aggiornare il valore della `NativeSwitchPageViewModel.IsSwitchOn` Proprietà. Pertanto, quando l'opzione è disattivata, `Entry` è disabilitata e quando l'opzione è attivata, `Entry` è abilitato. Gli screenshot seguenti illustrano questa funzionalità in ogni piattaforma:

![](xaml-images/native-switch-disabled.png "Native Switch Disabled")
![](xaml-images/native-switch-enabled.png "Native Switch Enabled")

Le associazioni bidirezionali sono supportate automaticamente purché la proprietà nativa implementi `INotifyPropertyChanged` o supporti l'osservazione chiave-valore (KVO) in iOS o sia un `DependencyProperty` in UWP. Tuttavia, molte visualizzazioni native non supportano la notifica delle modifiche alle proprietà. Per queste visualizzazioni, è possibile specificare un [`UpdateSourceEventName`](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valore di proprietà come parte dell'espressione di associazione. Questa proprietà deve essere impostata sul nome di un evento nella visualizzazione nativa che segnala quando la proprietà di destinazione è stata modificata. Quindi, quando cambia il valore del commutatore nativo, la `Binding` classe riceve una notifica che indica che l'utente ha modificato il valore dell'opzione e il `NativeSwitchPageViewModel.IsSwitchOn` valore della proprietà è stato aggiornato.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passaggio di argomenti a visualizzazioni native

Gli argomenti del costruttore possono essere passati alle viste native usando l' `x:Arguments` attributo con un' `x:Static` estensione di markup. Inoltre, i metodi factory di visualizzazione nativi ( `public static` metodi che restituiscono oggetti o valori dello stesso tipo della classe o della struttura che definiscono i metodi) possono essere chiamati specificando il nome del metodo usando l' `x:FactoryMethod` attributo e i relativi argomenti che usano l' `x:Arguments` attributo.

Nell'esempio di codice seguente vengono illustrate entrambe le tecniche:

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

Il [`UIFont.FromName`](xref:UIKit.UIFont.FromName*) metodo factory viene usato per impostare la [`UILabel.Font`](xref:UIKit.UILabel.Font) proprietà su un nuovo [`UIFont`](xref:UIKit.UIFont) in iOS. Il `UIFont` nome e la dimensione sono specificati dagli argomenti del metodo figlio dell' `x:Arguments` attributo.

Il [`Typeface.Create`](xref:Android.Graphics.Typeface.Create*) metodo factory viene usato per impostare la [`TextView.Typeface`](xref:Android.Widget.TextView.Typeface) proprietà su un nuovo [`Typeface`](xref:Android.Graphics.Typeface) in Android. Il `Typeface` nome e lo stile della famiglia vengono specificati dagli argomenti del metodo figlio dell' `x:Arguments` attributo.

Il [`FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) costruttore viene utilizzato per impostare la [`TextBlock.FontFamily`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) proprietà su un nuovo oggetto `FontFamily` nel piattaforma UWP (Universal Windows Platform) (UWP). Il `FontFamily` nome viene specificato dall'argomento del metodo che è un elemento figlio dell' `x:Arguments` attributo.

> [!NOTE]
> Gli argomenti devono corrispondere ai tipi richiesti dal costruttore o dal metodo factory.

Gli screenshot seguenti mostrano il risultato della specifica degli argomenti del costruttore e del metodo factory per impostare il tipo di carattere in visualizzazioni native diverse:

![](xaml-images/passing-arguments.png "Setting Fonts on Native Views")

Per ulteriori informazioni sul passaggio di argomenti in XAML, vedere [passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Riferimento a visualizzazioni native dal codice

Sebbene non sia possibile assegnare un nome a una visualizzazione nativa con l' `x:Name` attributo, è possibile recuperare un'istanza di visualizzazione nativa dichiarata in un file XAML dal relativo file code-behind in un progetto di accesso condiviso, purché la visualizzazione nativa sia un elemento figlio di un oggetto [`ContentView`](xref:Xamarin.Forms.ContentView) che specifica un `x:Name` valore di attributo. Quindi, all'interno delle direttive di compilazione condizionale nel file code-behind è necessario:

1. Recuperare il [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) valore della proprietà ed eseguirne il cast a un tipo specifico della piattaforma `NativeViewWrapper` .
1. Recuperare la `NativeViewWrapper.NativeElement` proprietà ed eseguirne il cast al tipo di visualizzazione nativo.

L'API nativa può quindi essere richiamata sulla visualizzazione nativa per eseguire le operazioni desiderate. Questo approccio offre anche il vantaggio che più visualizzazioni native XAML per diverse piattaforme possono essere elementi figlio dello stesso [`ContentView`](xref:Xamarin.Forms.ContentView) . Nell'esempio di codice seguente viene illustrata questa tecnica:

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

Nell'esempio precedente, le viste native per ogni piattaforma sono elementi figlio dei [`ContentView`](xref:Xamarin.Forms.ContentView) controlli, con il `x:Name` valore dell'attributo usato per recuperare l'oggetto `ContentView` nel code-behind:

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

[`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content)È possibile accedere alla proprietà per recuperare la visualizzazione nativa di cui è stato eseguito il wrapped come istanza specifica della piattaforma `NativeViewWrapper` . `NativeViewWrapper.NativeElement`Viene quindi eseguito l'accesso alla proprietà per recuperare la visualizzazione nativa come tipo nativo. L'API della visualizzazione nativa viene quindi richiamata per eseguire le operazioni desiderate.

I pulsanti nativi iOS e Android condividono lo stesso `OnButtonTap` gestore eventi, perché ogni pulsante nativo usa un `EventHandler` delegato in risposta a un evento Touch. Tuttavia, il piattaforma UWP (Universal Windows Platform) (UWP) utilizza un separato `RoutedEventHandler` , che a sua volta utilizza il `OnButtonTap` gestore eventi in questo esempio. Pertanto, quando si fa clic su un pulsante nativo, viene `OnButtonTap` eseguito il gestore dell'evento, che consente di ridimensionare e ruotare il controllo nativo contenuto all'interno dell'oggetto [`ContentView`](xref:Xamarin.Forms.ContentView) denominato `contentViewTextParent` . Gli screenshot seguenti illustrano ciò che si verifica in ogni piattaforma:

![](xaml-images/contentview.png "ContentView Containing a Native Control")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Sottoclassi di viste Native

Molte visualizzazioni native iOS e Android non sono adatte per la creazione di un'istanza in XAML perché usano metodi, anziché proprietà, per configurare il controllo. La soluzione a questo problema è la sottoclasse delle viste native nei wrapper che definiscono un'API più intuitiva che usa proprietà per configurare il controllo e che usa eventi indipendenti dalla piattaforma. Le visualizzazioni native di cui è stato eseguito il wrapped possono quindi essere inserite in un progetto asset condiviso (SAP) e racchiuse tra direttive di compilazione condizionale o inserite in progetti specifici della piattaforma e a cui viene fatto riferimento da XAML in un progetto di libreria .NET Standard.

Nell'esempio di codice seguente viene illustrata una Xamarin.Forms pagina che utilizza visualizzazioni native sottoclassi:

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

La pagina contiene un oggetto [`Label`](xref:Xamarin.Forms.Label) che Visualizza il frutto scelto dall'utente da un controllo nativo. L'oggetto viene `Label` associato alla `SubclassedNativeControlsPageViewModel.SelectedFruit` Proprietà. La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostata su una nuova istanza della `SubclassedNativeControlsPageViewModel` classe nel file code-behind, con la classe ViewModel che implementa l' `INotifyPropertyChanged` interfaccia.

La pagina contiene anche una visualizzazione selezione nativa per ogni piattaforma. Ogni visualizzazione nativa Visualizza la raccolta di frutti associando la relativa `ItemSource` proprietà alla `SubclassedNativeControlsPageViewModel.Fruits` raccolta. Ciò consente all'utente di selezionare un frutto, come illustrato nelle schermate seguenti:

![](xaml-images/sub-classed.png "Subclassed Native Views")

In iOS e Android i selezionatori nativi usano i metodi per configurare i controlli. Pertanto, questi selezionatori devono essere sottoclassati per esporre le proprietà in modo da renderli semplici da XAML. Nel piattaforma UWP (Universal Windows Platform) (UWP), `ComboBox` è già compatibile con XAML e pertanto non richiede la sottoclasse.

### <a name="ios"></a>iOS

L'implementazione di iOS sottoclassi la [`UIPickerView`](xref:UIKit.UIPickerView) vista ed espone le proprietà e un evento che può essere facilmente utilizzato da XAML:

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

La `MyUIPickerView` classe espone `ItemsSource` le `SelectedItem` proprietà e e un `SelectedItemChanged` evento. Un oggetto [`UIPickerView`](xref:UIKit.UIPickerView) richiede un [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) modello di dati sottostante, accessibile dalle `MyUIPickerView` proprietà e dall'evento. Il `UIPickerViewModel` modello di dati viene fornito dalla `PickerModel` classe:

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

La `PickerModel` classe fornisce l'archiviazione sottostante per la `MyUIPickerView` classe tramite la `Items` Proprietà. Ogni volta che viene modificato l'elemento selezionato in `MyUIPickerView` , [`Selected`](xref:UIKit.UIPickerViewModel.Selected*) viene eseguito il metodo, che aggiorna l'indice selezionato e genera l' `ItemChanged` evento. In questo modo si garantisce che la `SelectedItem` proprietà restituisca sempre l'ultimo elemento selezionato dall'utente. Inoltre, la `PickerModel` classe esegue l'override dei metodi utilizzati per configurare l' `MyUIPickerView` istanza di.

### <a name="android"></a>Android

L'implementazione di Android sottoclassi la [`Spinner`](xref:Android.Widget.Spinner) vista ed espone le proprietà e un evento che può essere facilmente utilizzato da XAML:

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

La `MySpinner` classe espone `ItemsSource` le `SelectedObject` proprietà e e un `ItemSelected` evento. Gli elementi visualizzati dalla `MySpinner` classe sono forniti dall'oggetto [`Adapter`](xref:Android.Widget.Adapter) associato alla visualizzazione e gli elementi vengono popolati in `Adapter` quando la `ItemsSource` proprietà viene impostata per la prima volta. Ogni volta che viene modificato l'elemento selezionato nella `MySpinner` classe, il `OnBindableSpinnerItemSelected` gestore eventi aggiorna la `SelectedObject` Proprietà.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come utilizzare viste native da Xamarin.Forms file XAML. Le proprietà e i gestori di eventi possono essere impostati sulle visualizzazioni native e possono interagire con le Xamarin.Forms visualizzazioni.

## <a name="related-links"></a>Collegamenti correlati

- [NativeSwitch (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Form nativi](~/xamarin-forms/platform/native-forms.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
