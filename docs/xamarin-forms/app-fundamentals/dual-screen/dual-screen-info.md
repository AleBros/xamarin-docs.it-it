---
title: Funzionalità del dispositivo dual screen Novell. Forms
description: Questa guida illustra come usare la classe DualScreenInfo di Xamarin.Forms per ottimizzare l'esperienza delle app per dispositivi con doppio schermo, ad esempio Surface Duo e Surface Neo.
ms.prod: xamarin
ms.assetid: dd5eb074-f4cb-4ab4-b47d-76f862ac7cfa
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 8dda1960fceaf7738c44d58b10149b8c20fa543f
ms.sourcegitcommit: 5b6d3bddf7148f8bb374de5657bdedc125d72ea7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78160600"
---
# <a name="xamarinforms-dual-screen-device-capabilities"></a>Funzionalità del dispositivo dual screen Novell. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/DualScreenDemos)

La classe `DualScreenInfo` consente di determinare il riquadro in cui si trova la visualizzazione, le dimensioni del riquadro, l'orientamento del dispositivo, l'angolo della cerniera e altro ancora.

## <a name="properties"></a>Proprietà

- `SpanningBounds`: nel layout esteso a due schermi, questa proprietà restituisce due rettangoli che indicano i limiti di ogni area visibile. Se la finestra non è estesa, verrà restituita una matrice vuota.
- `HingeBounds`: indica la posizione della cerniera sullo schermo.
- `IsLandscape`: indica se il dispositivo è in modalità orizzontale. Questa informazione è utile perché le API di orientamento native non segnalano correttamente l'orientamento quando un'applicazione è estesa.
- `SpanMode`: indica se il layout è in modalità Tall, Wide o SinglePane.

Viene inoltre generato un evento `PropertyChanged` in presenza di modifiche delle proprietà.

## <a name="poll-hinge-angle-on-android"></a>Recuperare l'angolo della cerniera in Android

La proprietà seguente è disponibile solo quando si accede a `DualScreenInfo` dal progetto della piattaforma Android:

- `GetHingeAngleAsync`: recupera l'angolo corrente della cerniera del dispositivo. Quando si usa il simulatore, è possibile impostare HingeAngle modificando il sensore di pressione.

Questa proprietà può essere usata da un renderer personalizzato Android:

```csharp
public class HingeAngleLabelRenderer : Xamarin.Forms.Platform.Android.FastRenderers.LabelRenderer
{
    System.Timers.Timer _hingeTimer;
    public HingeAngleLabelRenderer(Context context) : base(context)
    {
    }

    async void OnTimerElapsed(object sender, System.Timers.ElapsedEventArgs e)
    {
        if (_hingeTimer == null)
            return;

        _hingeTimer.Stop();
        var hingeAngle = await DualScreenInfo.Current.GetHingeAngleAsync();

        Device.BeginInvokeOnMainThread(() =>
        {
            if (_hingeTimer != null)
                Element.Text = hingeAngle.ToString();
        });

        if (_hingeTimer != null)
            _hingeTimer.Start();
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Label> e)
    {
        base.OnElementChanged(e);

        if (_hingeTimer == null)
        {
            _hingeTimer = new System.Timers.Timer(100);
            _hingeTimer.Elapsed += OnTimerElapsed;
            _hingeTimer.Start();
        }
    }

    protected override void Dispose(bool disposing)
    {
        if (_hingeTimer != null)
        {
            _hingeTimer.Elapsed -= OnTimerElapsed;
            _hingeTimer.Stop();
            _hingeTimer = null;
        }

        base.Dispose(disposing);
    }
}
```

## <a name="access-dualscreeninfo-in-your-application-window"></a>Accedere a DualScreenInfo nella finestra dell'applicazione

Il codice seguente illustra come accedere a `DualScreenInfo` per la finestra dell'applicazione:

```csharp
DualScreenInfo currentWindow = DualScreenInfo.Current;

// Retrieve absolute position of the hinge on the screen
var hingeBounds = currentWindow.HingeBounds;

// check if app window is spanned across two screens
if(currentWindow.SpanMode == TwoPaneViewMode.SinglePane)
{
    // window is only on one screen
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Tall)
{
    // window is spanned across two screens and oriented top-bottom
}
else if(currentWindow.SpanMode == TwoPaneViewMode.Wide)
{
    // window is spanned across two screens and oriented side-by-side
}

// Detect if any of the properties on DualScreenInfo change.
// This is useful to detect if the app window gets spanned
// across two screens or put on only one  
currentWindow.PropertyChanged += OnDualScreenInfoChanged;
```

## <a name="apply-dualscreeninfo-to-layouts"></a>Applicare DualScreenInfo ai layout

La classe `DualScreenInfo` include un costruttore che può accettare un layout e fornire informazioni sul layout in relazione ai dispositivi con doppio schermo:

```xaml
<Grid x:Name="grid" ColumnSpacing="0">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="{Binding Column1Width}" />
        <ColumnDefinition Width="{Binding Column2Width}" />
        <ColumnDefinition Width="{Binding Column3Width}" />
    </Grid.ColumnDefinitions>
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="End"
           Text="I should be on the left side of the hinge" />
    <Label FontSize="Large"
           VerticalOptions="Center"
           HorizontalOptions="Start"
           Grid.Column="2"
           Text="I should be on the right side of the hinge" />
</Grid>
```

```csharp
public partial class GridUsingDualScreenInfo : ContentPage
{
    public DualScreenInfo DualScreenInfo { get; }
    public double Column1Width { get; set; }
    public double Column2Width { get; set; }
    public double Column3Width { get; set; }

    public GridUsingDualScreenInfo()
    {
        InitializeComponent();
        DualScreenInfo = new DualScreenInfo(grid);
        BindingContext = this;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        DualScreenInfo.PropertyChanged += OnInfoPropertyChanged;
        UpdateColumns();
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        DualScreenInfo.PropertyChanged -= OnInfoPropertyChanged;
    }

    void UpdateColumns()
    {
        // Check if grid is on two screens
        if (DualScreenInfo.SpanningBounds.Length > 0)
        {
            // set the width of the first column to the width of the layout
            // that's on the left screen
            Column1Width = DualScreenInfo.SpanningBounds[0].Width;

            // set the middle column to the width of the hinge
            Column2Width = DualScreenInfo.HingeBounds.Width;

            // set the width of the third column to the width of the layout
            // that's on the right screen
            Column3Width = DualScreenInfo.SpanningBounds[1].Width;
        }
        else
        {
            Column1Width = 100;
            Column2Width = 0;
            Column3Width = 100;
        }

        OnPropertyChanged(nameof(Column1Width));
        OnPropertyChanged(nameof(Column2Width));
        OnPropertyChanged(nameof(Column3Width));

    }

    void OnInfoPropertyChanged(object sender, System.ComponentModel.PropertyChangedEventArgs e)
    {
        UpdateColumns();
    }
}
```

Lo screenshot seguente mostra il layout risultante:

![](dual-screen-info-images/grid-on-two-screens.png "Positioning Grid on Two Screens")

## <a name="related-links"></a>Collegamenti correlati

- [DualScreen (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/DualScreenDemos)
