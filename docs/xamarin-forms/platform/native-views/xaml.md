---
title: Visualizzazioni native in XAML
description: Visualizzazioni native da iOS, Android e la piattaforma universale di Windows possono essere indicate direttamente dai file XAML di xamarin. Forms. Le proprietà e gestori di eventi possono essere impostati su visualizzazioni native e possono interagire con le visualizzazioni di xamarin. Forms. Questo articolo viene illustrato come utilizzare le visualizzazioni native dai file XAML di xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: 3c4fa085c9fdf17cdc256d9710c23911bb60d584
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770636"
---
# <a name="native-views-in-xaml"></a>Visualizzazioni native in XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)

_È possibile fare riferimento direttamente alle viste native di iOS, Android e piattaforma UWP (Universal Windows Platform) dai file XAML Novell. Forms. Le proprietà e i gestori di eventi possono essere impostati sulle visualizzazioni native e possono interagire con le visualizzazioni Novell. Forms. Questo articolo illustra come usare le visualizzazioni native dei file XAML Novell. Forms._

Nell'articolo vengono trattati gli argomenti seguenti:

- [Utilizzo di visualizzazioni native](#consuming) : il processo per l'utilizzo di una visualizzazione nativa da XAML.
- [Uso di associazioni native](#native_bindings) : data binding da e verso le proprietà delle visualizzazioni native.
- [Passaggio di argomenti per le visualizzazioni native](#passing_arguments) : passaggio di argomenti ai costruttori di visualizzazione nativa e la chiamata di metodi factory visualizzazione nativa.
- [Riferimento per le visualizzazioni native da codice](#native_view_code) : recupero di istanze di visualizzazione nativa dichiarati in un file XAML, dal relativo file code-behind.
- [Creazione di una sottoclasse le visualizzazioni native](#subclassing) : creazione di una sottoclasse le visualizzazioni native per definire un'API di facile integrazione con XAML.  

<a name="overview" />

## <a name="overview"></a>Panoramica

Per incorporare una visualizzazione nativa in un file XAML di xamarin. Forms:

1. Aggiungere un `xmlns` dichiarazione dello spazio dei nomi nel file XAML per lo spazio dei nomi che contiene la visualizzazione nativa.
1. Creare un'istanza della visualizzazione nativa nel file XAML.

> [!IMPORTANT]
> Il codice XAML compilato deve essere disabilitato per tutte le pagine XAML che usano viste native. Questa operazione può essere eseguita decorando la classe code-behind per la pagina XAML con l'attributo `[XamlCompilation(XamlCompilationOptions.Skip)]`. Per altre informazioni sulla compilazione XAML, vedere [compilazione XAML in Novell. Forms](~/xamarin-forms/xaml/xamlc.md).

Per fare riferimento a una visualizzazione nativa da un file code-behind, è necessario usare un progetto di Asset condivisi (SAP) e inserire il codice specifico della piattaforma con direttive di compilazione condizionale. Per altre informazioni, vedere [che fa riferimento per le visualizzazioni Native da codice](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Utilizzo di visualizzazioni Native

Esempio di codice seguente viene illustrato l'uso di visualizzazioni native per ogni piattaforma per un xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage):

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

E specificare anche il `clr-namespace` e `assembly` per uno spazio dei nomi di visualizzazione nativa, un `targetPlatform` deve anche essere specificato. Questo deve essere impostato su uno dei valori del [ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform) enumerazione e verrà in genere impostato `iOS`, `Android`, o `Windows`. In fase di esecuzione, il parser XAML ignorerà qualsiasi prefisso di spazio dei nomi XML che hanno un `targetPlatform` immesso non corrisponde la piattaforma su cui è in esecuzione l'applicazione.

Ogni dichiarazione dello spazio dei nomi è utilizzabile per fare riferimento a qualsiasi classe o struttura dallo spazio dei nomi specificato. Ad esempio, il `ios` dichiarazione dello spazio dei nomi può essere utilizzato per fare riferimento a qualsiasi classe o struttura da iOS `UIKit` dello spazio dei nomi. Proprietà della vista nativa può essere impostata tramite XAML, ma i tipi di oggetto e proprietà devono corrispondere. Ad esempio, il `UILabel.TextColor` è impostata su `UIColor.Red` usando la `x:Static` estensione di markup e il `ios` dello spazio dei nomi.

Proprietà associabile e le proprietà associabili associate possono anche nelze nastavit visualizzazioni native tramite la `Class.BindableProperty="value"` sintassi. Ogni visualizzazione nativa viene eseguito il wrapping in una specifica della piattaforma `NativeViewWrapper` istanza, che deriva dal [ `Xamarin.Forms.View` ](xref:Xamarin.Forms.View) classe. L'impostazione di una proprietà associabile o una proprietà associabile associata in una vista native trasferisce il valore della proprietà per il wrapper. Ad esempio, un layout orizzontale centrato può essere specificato impostando `View.HorizontalOptions="Center"` nella visualizzazione nativa.

> [!NOTE]
> Si noti che gli stili possono essere utilizzati con le visualizzazioni native, perché gli stili possono avere come destinazione solo le proprietà che sono supportate da `BindableProperty` oggetti.

Costruttori di widget Android richiedono in genere di Android `Context` dell'oggetto come argomento e questo può essere resi disponibili tramite una proprietà statica nel `MainActivity` classe. Pertanto, quando si crea un widget di Android in XAML, il `Context` oggetto in genere deve essere passato al costruttore del widget tramite il `x:Arguments` dell'attributo con un `x:Static` estensione di markup. Per altre informazioni, vedere [passando gli argomenti per le visualizzazioni Native](#passing_arguments).

> [!NOTE]
> Si noti che una visualizzazione nativa con denominazione `x:Name` non è possibile in un progetto di libreria .NET Standard o un progetto di Asset condivisi (SAP). In questo modo genererà una variabile del tipo nativo, che provoca un errore di compilazione. Tuttavia, le visualizzazioni native possono essere incluso in `ContentView` istanze e recuperato nel file code-behind, condizione che un SAP è in uso. Per altre informazioni, vedere [che fa riferimento a una visualizzazione Native da codice](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Binding nativo

Data binding utilizzato per sincronizzare un'interfaccia utente con l'origine dati e semplifica le operazioni come un'applicazione xamarin. Forms Visualizza e interagisce con i relativi dati. Condizione che implementa l'oggetto di origine di `INotifyPropertyChanged` modifiche all'interfaccia, nelle *origine* oggetto viene automaticamente effettuato il push il *destinazione* oggetto dal framework di associazione e le modifiche nel *destinazione* oggetti possono facoltativamente essere inseriti le *origine* oggetto.

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

La pagina contiene un [ `Entry` ](xref:Xamarin.Forms.Entry) cui [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà viene associata ai `NativeSwitchPageViewModel.IsSwitchOn` proprietà. Il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostata su una nuova istanza della `NativeSwitchPageViewModel` classe nel file code-behind, con la classe ViewModel che implementa il `INotifyPropertyChanged` interfaccia.

Questa pagina contiene anche un commutatore nativo per ogni piattaforma. Ogni commutatore nativa Usa un [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay) dell'associazione per aggiornare il valore della `NativeSwitchPageViewModel.IsSwitchOn` proprietà. Pertanto, quando l'opzione è disattivata, la `Entry` è disabilitata, e quando l'opzione è on, il `Entry` è abilitata. Le schermate seguenti illustrano questa funzionalità in ogni piattaforma:

![](xaml-images/native-switch-disabled.png "Commutatore nativo Disabled")
![](xaml-images/native-switch-enabled.png "nativa commutatore abilitato")

Le associazioni bidirezionali sono automaticamente supportate condizione che implementa la proprietà nativa `INotifyPropertyChanged`, o supporta l'osservazione di chiave-valore (KVO) in iOS o non sia un `DependencyProperty` nella piattaforma UWP. Tuttavia, molte visualizzazioni native non supportano la notifica di modifiche. Per queste viste, è possibile specificare una [ `UpdateSourceEventName` ](xref:Xamarin.Forms.Binding.UpdateSourceEventName) valore della proprietà come parte dell'espressione di associazione. Questa proprietà deve essere impostata sul nome di un evento nella visualizzazione nativa che segnala quando viene modificata la proprietà di destinazione. Quindi, quando il valore dell'opzione native cambia, il `Binding` classe viene informata che l'utente ha modificato il valore dell'opzione e il `NativeSwitchPageViewModel.IsSwitchOn` viene aggiornato il valore di proprietà.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passaggio di argomenti per le visualizzazioni Native

Gli argomenti del costruttore possono essere passati a visualizzazioni native usando il `x:Arguments` dell'attributo con un `x:Static` estensione di markup. Inoltre, metodi factory visualizzazione nativo (`public static` i metodi che restituiscono oggetti o valori dello stesso tipo della classe o struttura che definisce i metodi) può essere chiamato da specifica del metodo nome utilizzando il `x:FactoryMethod` attributo e i relativi argomenti uso di `x:Arguments` attributo.

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

Il [ `UIFont.FromName` ](xref:UIKit.UIFont.FromName*) metodo factory utilizzato per impostare il [ `UILabel.Font` ](xref:UIKit.UILabel.Font) proprietà a un nuovo [ `UIFont` ](xref:UIKit.UIFont) in iOS. Il `UIFont` specificati da argomenti del metodo che sono figli del nome e le dimensioni di `x:Arguments` attributo.

Il [ `Typeface.Create` ](xref:Android.Graphics.Typeface.Create*) metodo factory utilizzato per impostare il [ `TextView.Typeface` ](xref:Android.Widget.TextView.Typeface) proprietà a un nuovo [ `Typeface` ](xref:Android.Graphics.Typeface) in Android. Il `Typeface` specificati da argomenti del metodo che sono figli del cognome e lo stile di `x:Arguments` attributo.

Il [ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) costruttore viene utilizzato per impostare il [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) proprietà a un nuovo `FontFamily` in Universal Windows Platform (UWP). Il `FontFamily` nome viene specificato dall'argomento del metodo è un figlio di `x:Arguments` attributo.

> [!NOTE]
> Gli argomenti devono corrispondere ai tipi necessari per il costruttore o al metodo factory.

Gli screenshot seguenti mostrano il risultato della specifica gli argomenti di metodi e costruttori factory per impostare il tipo di carattere per le visualizzazioni native diversi:

![](xaml-images/passing-arguments.png "Impostazione tipi di carattere in visualizzazioni Native")

Per altre informazioni sul passaggio di argomenti in XAML, vedere [passando gli argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Riferimento per le visualizzazioni Native da codice

Anche se non è possibile assegnare un nome di una visualizzazione nativa con il `x:Name` attributo, è possibile recuperare un'istanza di visualizzazione nativa dichiarata in un file XAML dal relativo file code-behind in un progetto di accesso condiviso, condizione che la visualizzazione nativa è un elemento figlio di un [ `ContentView` ](xref:Xamarin.Forms.ContentView) che specifica un `x:Name` valore dell'attributo. Quindi, all'interno di direttive di compilazione condizionale nel file code-behind è necessario:

1. Recuperare il [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) proprietà valore ed eseguirne il cast a una specifica della piattaforma `NativeViewWrapper` tipo.
1. Recuperare il `NativeViewWrapper.NativeElement` proprietà ed eseguirne il cast al tipo di visualizzazione nativa.

L'API nativa può quindi essere richiamato sulle visualizzazione nativa per eseguire le operazioni desiderate. Questo approccio offre inoltre il vantaggio che più visualizzazioni native XAML per piattaforme diverse possono essere elementi figlio dello stesso [ `ContentView` ](xref:Xamarin.Forms.ContentView). L'esempio di codice seguente illustra questa tecnica:

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

Nell'esempio precedente, le visualizzazioni native per ogni piattaforma sono elementi figlio del [ `ContentView` ](xref:Xamarin.Forms.ContentView) controlli, con il `x:Name` valore dell'attributo viene usato per recuperare il `ContentView` nel code-behind:

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

Il [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) per recuperare la visualizzazione nativa sottoposta a wrapping come una specifica della piattaforma si accede alla proprietà `NativeViewWrapper` istanza. Il `NativeViewWrapper.NativeElement` proprietà sarà possibile accedere per recuperare la visualizzazione nativa come relativo tipo nativo. API nativa della visualizzazione viene quindi richiamato per eseguire le operazioni desiderate.

IOS e Android native pulsanti condividono lo stesso `OnButtonTap` gestore dell'evento, perché ogni pulsante nativo utilizza un `EventHandler` delegare in risposta a un evento di tocco. Tuttavia, Universal Windows Platform (UWP) usa un oggetto separato `RoutedEventHandler`, che a sua volta utilizza la `OnButtonTap` gestore dell'evento in questo esempio. Pertanto, quando viene selezionato un pulsante native, il `OnButtonTap` gestore eventi viene eseguito, che ridimensiona e ruota del controllo nativo contenuto all'interno di [ `ContentView` ](xref:Xamarin.Forms.ContentView) denominato `contentViewTextParent`. Gli screenshot seguenti illustrano questo corso in ogni piattaforma:

![](xaml-images/contentview.png "ContentView contenente un controllo nativo")

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

La pagina contiene un [ `Label` ](xref:Xamarin.Forms.Label) che visualizza i frutti scelti dall'utente da un controllo nativo. Il `Label` associa il `SubclassedNativeControlsPageViewModel.SelectedFruit` proprietà. Il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) della pagina è impostata su una nuova istanza della `SubclassedNativeControlsPageViewModel` classe nel file code-behind, con la classe ViewModel che implementa il `INotifyPropertyChanged` interfaccia.

La pagina contiene anche una visualizzazione di selezione nativo per ogni piattaforma. Ogni visualizzazione nativa consente di visualizzare la raccolta di frutta mediante l'associazione relativa `ItemSource` proprietà per il `SubclassedNativeControlsPageViewModel.Fruits` raccolta. In questo modo l'utente può selezionare un fruit, come illustrato negli screenshot seguenti:

![](xaml-images/sub-classed.png "Viste native sottoclassi")

In iOS e Android i controlli di selezione native usare metodi per configurare i controlli. Di conseguenza, questi controlli di selezione deve essere sottoclassati per esporre le proprietà per fare in modo facile integrazione con XAML. In Universal Windows Platform (UWP), il `ComboBox` è già facile integrazione con XAML e pertanto non richiede la creazione di sottoclassi.

### <a name="ios"></a>iOS

Le sottoclassi di implementazione iOS il [ `UIPickerView` ](xref:UIKit.UIPickerView) visualizzare ed espone proprietà e un evento che può essere usato facilmente da XAML:

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

Il `MyUIPickerView` classe espone `ItemsSource` e `SelectedItem` delle proprietà e un `SelectedItemChanged` evento. Oggetto [ `UIPickerView` ](xref:UIKit.UIPickerView) richiede un oggetto sottostante [ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel) modello di dati, che è accessibile tramite il `MyUIPickerView` proprietà ed eventi. Il `UIPickerViewModel` modello di dati avviene tramite il `PickerModel` classe:

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

Il `PickerModel` classe fornisce l'archiviazione sottostante per il `MyUIPickerView` (classe), tramite il `Items` proprietà. Ogni volta che l'elemento selezionato nel `MyUIPickerView` le modifiche, il [ `Selected` ](xref:UIKit.UIPickerViewModel.Selected*) metodo viene eseguito, che aggiorna l'indice selezionato e viene attivato il `ItemChanged` evento. Ciò garantisce che il `SelectedItem` proprietà restituirà sempre l'ultimo elemento selezionato dall'utente. Inoltre, il `PickerModel` classe esegue l'override di metodi che vengono usati per il programma di installazione di `MyUIPickerView` istanza.

### <a name="android"></a>Android

Le sottoclassi di implementazione di Android le [ `Spinner` ](xref:Android.Widget.Spinner) visualizzare ed espone proprietà e un evento che può essere usato facilmente da XAML:

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

Il `MySpinner` classe espone `ItemsSource` e `SelectedObject` delle proprietà e un `ItemSelected` evento. Gli elementi visualizzati dal `MySpinner` classe forniti dal [ `Adapter` ](xref:Android.Widget.Adapter) associato alla visualizzazione e gli elementi vengono popolati nel `Adapter` quando la `ItemsSource` viene innanzitutto impostata. Ogni volta che l'elemento selezionato nel `MySpinner` classe modifiche, il `OnBindableSpinnerItemSelected` gestore eventi aggiorna il `SelectedObject` proprietà.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come utilizzare le visualizzazioni native dai file XAML di xamarin. Forms. Le proprietà e gestori di eventi possono essere impostati su visualizzazioni native e possono interagire con le visualizzazioni di xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [NativeSwitch (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeswitch)
- [Forms2Native (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/forms2native)
- [NativeViewInsideContentView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-nativeviewinsidecontentview)
- [SubclassedNativeControls (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeviews-subclassednativecontrols)
- [Form nativi](~/xamarin-forms/platform/native-forms.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
