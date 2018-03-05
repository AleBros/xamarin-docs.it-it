---
title: Visualizzazioni native in XAML
description: "Viste native da iOS, Android e la piattaforma UWP possono fare riferimento direttamente da file XAML di xamarin. Forms. Le proprietà e i gestori eventi possono essere impostati nativo viste, e possono interagire con le visualizzazioni di xamarin. Forms. In questo articolo viene illustrato come utilizzare le viste native da file XAML di xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: fc44b2a6080832d11c610661244172ad4a6a0716
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="native-views-in-xaml"></a>Visualizzazioni native in XAML

_Viste native da iOS, Android e la piattaforma UWP possono fare riferimento direttamente da file XAML di xamarin. Forms. Le proprietà e i gestori eventi possono essere impostati nativo viste, e possono interagire con le visualizzazioni di xamarin. Forms. In questo articolo viene illustrato come utilizzare le viste native da file XAML di xamarin. Forms._

In questo articolo vengono illustrati gli argomenti seguenti:

- [Utilizzo di viste native](#consuming) : il processo per l'utilizzo di una vista nativa da XAML.
- [Uso di associazioni native](#native_bindings) : dati di associazione da e verso le proprietà delle viste native.
- [Passaggio di argomenti a viste native](#passing_arguments) : passaggio di argomenti ai costruttori di visualizzazione e la chiamata di metodi factory visualizzazione nativo.
- [Riferimenti a viste native da codice](#native_view_code) : recupero di istanze native vista dichiarati in un file XAML, dal relativo file code-behind.
- [Creazione di sottoclassi viste native](#subclassing) : sottoclassi viste native per definire un'API XAML-semplice.  

<a name="overview" />

## <a name="overview"></a>Panoramica

Per incorporare una visualizzazione nativa in un file XAML di xamarin. Forms:

1. Aggiungere un `xmlns` dichiarazione dello spazio dei nomi nel file XAML per lo spazio dei nomi contenente la vista nativa.
1. Creare un'istanza della visualizzazione nativa nel file XAML.

> [!NOTE]
> XAMLC deve essere disabilitata per tutte le pagine XAML che utilizzano viste native.

Per fare riferimento a una vista nativa da un file code-behind, è necessario utilizzare un progetto Asset condiviso (SAP) e inserire il codice specifico della piattaforma con le direttive di compilazione condizionale. Per ulteriori informazioni vedere [che fa riferimento a viste Native da codice](#native_view_code).

<a name="consuming" />

## <a name="consuming-native-views"></a>Utilizzo di viste Native

Esempio di codice seguente viene illustrato l'utilizzo di viste native per ogni piattaforma per un xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static formsandroid:Forms.Context}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

Oltre a specificare il `clr-namespace` e `assembly` per uno spazio dei nomi di visualizzazione nativo, un `targetPlatform` deve anche essere specificato. Impostare questa proprietà su uno dei valori del [ `TargetPlatform` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TargetPlatform/) enumerazione e in genere verrà impostato su `iOS`, `Android`, o `Windows`. In fase di esecuzione, il parser XAML ignorerà qualsiasi prefisso di spazio dei nomi XML che hanno un `targetPlatform` che non corrisponde alla piattaforma in cui è in esecuzione l'applicazione.

Ogni dichiarazione di spazio dei nomi consente di fare riferimento a qualsiasi classe o struttura dallo spazio dei nomi specificato. Ad esempio, il `ios` dichiarazione dello spazio dei nomi può essere utilizzato per fare riferimento a qualsiasi classe o struttura da iOS `UIKit` dello spazio dei nomi. Proprietà della vista nativa può essere impostata tramite XAML, ma i tipi di proprietà e oggetto devono corrispondere. Ad esempio, il `UILabel.TextColor` è impostata su `UIColor.Red` utilizzando il `x:Static` estensione di markup e `ios` dello spazio dei nomi.

Proprietà associabili e le proprietà associabili associate anche impostare visualizzazioni native tramite il `Class.BindableProperty="value"` sintassi. Viene eseguito il wrapping di ogni visualizzazione nativo in una specifica della piattaforma `NativeViewWrapper` istanza, che deriva dal [ `Xamarin.Forms.View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe. L'impostazione di una proprietà associabile o una proprietà associabile associata in una vista native trasferisce il valore della proprietà per il wrapper. Ad esempio, un layout orizzontale centrato può essere specificato impostando `View.HorizontalOptions="Center"` nella vista nativa.

> [!NOTE]
> Si noti che gli stili possono essere utilizzati con visualizzazioni native, perché gli stili possono avere come destinazione solo le proprietà che sono supportate da `BindableProperty` oggetti.

Costruttori di widget Android richiedono in genere di Android `Context` oggetto come argomento e questo è disponibile attraverso il `Xamarin.Forms.Platform.Android.Forms.Context` oggetto. Pertanto, quando si crea un widget di Android in XAML, il `Context` oggetto in genere deve essere passato al costruttore del widget utilizzando la `x:Arguments` attributo con un `x:Static` estensione di markup. Per ulteriori informazioni, vedere [il passaggio di argomenti da visualizzazioni Native](#passing_arguments).

> [!NOTE]
> Si noti che una vista nativa con denominazione `x:Name` non è possibile in un progetto libreria di classe portabile (PCL) o in un progetto Asset condiviso (SAP). In questo modo genererà una variabile del tipo nativo, che provoca un errore di compilazione. Tuttavia, le visualizzazioni native possono essere incluso in `ContentView` istanze e recuperato nel file code-behind, condizione che venga utilizzato un SAP. Per ulteriori informazioni, vedere [che fa riferimento a una visualizzazione Native da codice](#native_view_code).

<a name="native_bindings" />

## <a name="native-bindings"></a>Associazioni native

Associazione di dati utilizzato per sincronizzare un'interfaccia utente con l'origine dati e semplifica le modalità con cui un'applicazione di xamarin. Forms consente di visualizzare e interagisce con i relativi dati. Condizione che implementa l'oggetto di origine di `INotifyPropertyChanged` modifiche all'interfaccia, nel *origine* oggetto vengono inseriti automaticamente per il *destinazione* oggetto dal framework di associazione e le modifiche nel *destinazione* oggetto facoltativamente: Impossibile inviare il *origine* oggetto.

Proprietà delle viste native è inoltre possibile utilizzare l'associazione dati. Esempio di codice seguente viene illustrata l'associazione di dati utilizzando le proprietà delle viste native:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
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
        <androidWidget:Switch x:Arguments="{x:Static formsAndroid:Forms.Context}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

La pagina contiene un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) cui [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) proprietà viene associata al `NativeSwitchPageViewModel.IsSwitchOn` proprietà. Il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) della pagina è impostata su una nuova istanza di `NativeSwitchPageViewModel` classe nel file di codice, con la classe che implementa ViewModel il `INotifyPropertyChanged` interfaccia.

La pagina contiene inoltre un commutatore nativo per ogni piattaforma. Ogni commutatore nativa Usa un [ `TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) associazione aggiorna il valore della `NativeSwitchPageViewModel.IsSwitchOn` proprietà. Pertanto, quando l'opzione è disattivata, il `Entry` è disabilitato, e quando l'opzione è on, il `Entry` è abilitata. La schermata seguente mostra questa funzionalità in ogni piattaforma:

![](xaml-images/native-switch-disabled.png "Commutatore nativo disabilitato")
![](xaml-images/native-switch-enabled.png "commutatore nativo abilitato")

Le associazioni bidirezionali sono automaticamente supportate condizione che implementa la proprietà nativa `INotifyPropertyChanged`, supporta l'osservazione di chiave-valore (KVO) in iOS oppure non è un `DependencyProperty` in UWP. Tuttavia, molte visualizzazioni native non supportano la notifica di modifiche. Per queste viste, è possibile specificare un [ `UpdateSourceEventName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.UpdateSourceEventName/) valore della proprietà come parte dell'espressione di associazione. Questa proprietà deve essere impostata sul nome di un evento nella visualizzazione nativa che segnala quando viene modificata la proprietà di destinazione. Quindi, quando il valore dell'opzione native cambia, il `Binding` classe riceve una notifica che l'utente ha modificato il valore dell'opzione e `NativeSwitchPageViewModel.IsSwitchOn` viene aggiornato il valore di proprietà.

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>Passaggio di argomenti alle visualizzazioni Native

Gli argomenti del costruttore possono essere passati a visualizzazioni native tramite il `x:Arguments` attributo con un `x:Static` estensione di markup. Inoltre, metodi factory visualizzazione nativo (`public static` metodi che restituiscono oggetti o valori dello stesso tipo della classe o struttura che definisce i metodi) può essere chiamato da specifica del metodo nome usando il `x:FactoryMethod` attributo e i relativi argomenti utilizzo di `x:Arguments` attributo.

L'esempio di codice seguente illustra entrambe le tecniche:

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
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
        <androidWidget:TextView x:Arguments="{x:Static formsAndroid:Forms.Context}"
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

Il [ `UIFont.FromName` ](https://developer.xamarin.com/api/member/UIKit.UIFont.FromName/) metodo factory utilizzato per impostare il [ `UILabel.Font` ](https://developer.xamarin.com/api/property/UIKit.UILabel.Font/) proprietà a un nuovo [ `UIFont` ](https://developer.xamarin.com/api/type/UIKit.UIFont/) in iOS. Il `UIFont` specificati dagli argomenti di metodo che sono figli del nome e le dimensioni di `x:Arguments` attributo.

Il [ `Typeface.Create` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/p/System.String/Android.Graphics.TypefaceStyle/) metodo factory utilizzato per impostare il [ `TextView.Typeface` ](https://developer.xamarin.com/api/property/Android.Widget.TextView.Typeface/) proprietà a un nuovo [ `Typeface` ](https://developer.xamarin.com/api/type/Android.Graphics.Typeface/) in Android. Il `Typeface` nome famiglia e lo stile specificati dagli argomenti che sono figli del metodo di `x:Arguments` attributo.

Il [ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily) costruttore viene utilizzato per impostare il [ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily) proprietà a un nuovo `FontFamily` sulla piattaforma UWP (Universal Windows). Il `FontFamily` nome è specificato dall'argomento del metodo è un figlio di `x:Arguments` attributo.

> [!NOTE]
> **Nota**: argomenti devono corrispondere i tipi necessari per il costruttore o metodo factory.

Le schermate seguenti mostrano il risultato della specifica gli argomenti del costruttore e del metodo factory per impostare il tipo di carattere in diverse visualizzazioni native:

![](xaml-images/passing-arguments.png "Impostare i caratteri nelle viste Native")

Per ulteriori informazioni sul passaggio di argomenti in XAML, vedere [il passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md).

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>Riferimenti a viste Native da codice

Anche se non è possibile assegnare un nome di una vista nativa con il `x:Name` attributo, è possibile recuperare un'istanza di visualizzazione nativo dichiarata in un file XAML dal relativo file code-behind in un progetto di accesso condiviso, a condizione che la vista nativa è un elemento figlio di un [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) che specifica un `x:Name` valore dell'attributo. Quindi, all'interno di direttive di compilazione condizionale nel file code-behind è necessario:

1. Recuperare il [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) proprietà valore ed eseguirne il cast a un oggetto specifico della piattaforma `NativeViewWrapper` tipo.
1. Recuperare il `NativeViewWrapper.NativeElement` proprietà ed eseguirne il cast al tipo di visualizzazione nativo.

L'API nativa può quindi essere richiamata nella vista nativa per eseguire le operazioni desiderate. Questo approccio offre inoltre il vantaggio che più visualizzazioni native di XAML per piattaforme diverse possono essere elementi figlio dello stesso [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/). L'esempio di codice seguente illustra questa tecnica:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static formsAndroid:Forms.Context}"
                Text="Text in a TextView" />
            <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static formsAndroid:Forms.Context}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>

```

Nell'esempio precedente, le visualizzazioni native per ogni piattaforma sono elementi figlio di [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) controlli, con la `x:Name` valore dell'attributo utilizzato per recuperare il `ContentView` nel code-behind:

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

Il [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) si accede alla proprietà per recuperare una visualizzazione sottoposta a wrapping native come un specifico della piattaforma `NativeViewWrapper` istanza. Il `NativeViewWrapper.NativeElement` quindi accedere alla proprietà per recuperare la vista native come il tipo nativo. API nativa della vista viene quindi richiamato per eseguire le operazioni desiderate.

La iOS e Android pulsanti native condividono lo stesso `OnButtonTap` gestore eventi, poiché ogni pulsante nativo richiede un `EventHandler` delegato in risposta a un evento di tocco. Tuttavia, la piattaforma UWP (Universal Windows) usa un apposito `RoutedEventHandler`, che a sua volta Usa il `OnButtonTap` gestore dell'evento in questo esempio. Pertanto, quando viene selezionato un pulsante nativo, il `OnButtonTap` gestore eventi viene eseguito, che ridimensiona e ruota il controllo nativo contenuto all'interno di [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) denominato `contentViewTextParent`. Le schermate seguenti illustrano questo si verifica in ogni piattaforma:

![](xaml-images/contentview.png "ContentView contenente un controllo nativo")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>Creazione di sottoclassi viste Native

Molti iOS e Android viste native non sono adatte per creare un'istanza in XAML, perché utilizzano metodi, anziché le proprietà, per impostare il controllo. Per risolvere questo problema è a viste native sottoclasse wrapper che definiscono un'altre API XAML-semplice che utilizza le proprietà per il controllo del programma di installazione e che utilizza eventi indipendenti dalla piattaforma. Le viste native sottoposta a wrapping possono essere inserite in un condiviso Asset progetto SAP () e racchiuso tra le direttive di compilazione condizionale, o inserire nei progetti specifici della piattaforma e a cui fa riferimento da XAML in un progetto libreria di classe portabile (PCL).

Esempio di codice riportato di seguito viene illustrato come sottoclasse di una pagina di xamarin. Forms che utilizza visualizzazioni native:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:formsAndroid="clr-namespace:Xamarin.Forms;assembly=Xamarin.Forms.Platform.Android;targetPlatform=Android"
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
        <androidLocal:MySpinner x:Arguments="{x:Static formsAndroid:Forms.Context}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

La pagina contiene un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) che consente di visualizzare il frutto scelto dall'utente da un controllo nativo. Il `Label` associa il `SubclassedNativeControlsPageViewModel.SelectedFruit` proprietà. Il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) della pagina è impostata su una nuova istanza di `SubclassedNativeControlsPageViewModel` classe nel file di codice, con la classe che implementa ViewModel il `INotifyPropertyChanged` interfaccia.

La pagina contiene inoltre una visualizzazione della selezione nativo per ogni piattaforma. Ogni visualizzazione nativa consente di visualizzare la raccolta di frutta mediante l'associazione relativa `ItemSource` proprietà per il `SubclassedNativeControlsPageViewModel.Fruits` insieme. In questo modo l'utente può selezionare un frutta, come illustrato nelle schermate seguenti:

![](xaml-images/sub-classed.png "Viste Native sottoclasse")

In iOS e Android il prelievo native utilizza i metodi per i controlli del programma di installazione. Di conseguenza, questi controlli di selezione deve essere sottoclassati per esporre le proprietà in modo da renderli XAML-semplice. Nel Windows piattaforma UWP (Universal), il `ComboBox` è già XAML-semplice e pertanto non richiede la creazione di sottoclassi.

### <a name="ios"></a>iOS

Le sottoclassi di implementazione iOS il [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) , visualizzazione ed espone proprietà e un evento che può essere facilmente utilizzato da XAML:

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

Il `MyUIPickerView` classe espone `ItemsSource` e `SelectedItem` , proprietà e un `SelectedItemChanged` evento. A [ `UIPickerView` ](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) richiede un oggetto sottostante [ `UIPickerViewModel` ](https://developer.xamarin.com/api/type/UIKit.UIPickerViewModel/) modello di dati, che avviene mediante il `MyUIPickerView` proprietà ed eventi. Il `UIPickerViewModel` modello di dati viene eseguito il `PickerModel` classe:

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

Il `PickerModel` classe fornisce l'archiviazione sottostante per il `MyUIPickerView` (classe), tramite il `Items` proprietà. Ogni volta che l'elemento selezionato nel `MyUIPickerView` modifiche, il [ `Selected` ](https://developer.xamarin.com/api/member/UIKit.UIPickerViewModel.Selected/) esecuzione del metodo, che aggiorna l'indice selezionato e viene attivato il `ItemChanged` evento. In questo modo il `SelectedItem` proprietà restituisce sempre l'ultimo elemento selezionato dall'utente. Inoltre, il `PickerModel` classe esegue l'override di metodi che vengono utilizzati per il programma di installazione di `MyUIPickerView` istanza.

### <a name="android"></a>Android

Le sottoclassi di implementazione Android di [ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/) , visualizzazione ed espone proprietà e un evento che può essere facilmente utilizzato da XAML:

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

Il `MySpinner` classe espone `ItemsSource` e `SelectedObject` , proprietà e un `ItemSelected` evento. Gli elementi visualizzati dal `MySpinner` forniti dalla classe di [ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.Adapter/) associato alla visualizzazione e gli elementi vengono popolati nel `Adapter` quando il `ItemsSource` viene innanzitutto impostata. Ogni volta che l'elemento selezionato nel `MySpinner` classe modifiche, il `OnBindableSpinnerItemSelected` gestore eventi aggiorna il `SelectedObject` proprietà.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare le viste native da file XAML di xamarin. Forms. Le proprietà e i gestori eventi possono essere impostati nativo viste, e possono interagire con le visualizzazioni di xamarin. Forms.


## <a name="related-links"></a>Collegamenti correlati

- [NativeSwitch (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeSwitch/)
- [Forms2Native (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Forms2Native/)
- [NativeViewInsideContentView (sample)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeViewInsideContentView/)
- [SubclassedNativeControls (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/SubclassedNativeControls/)
- [Form nativi](~/xamarin-forms/platform/native-forms.md)
- [Passaggio di argomenti in XAML](~/xamarin-forms/xaml/passing-arguments.md)
