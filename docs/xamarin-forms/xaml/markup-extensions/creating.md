---
title: Creazione di estensioni di Markup XAML
description: In questo articolo viene illustrato come definire la propria personalizzate estensioni di markup XAML di xamarin. Forms. Un'estensione di markup XAML è una classe che implementa l'interfaccia IMarkupExtension IMarkupExtension.
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: b185ea3b7260ff2be8a4dec5dc713f24dc6e6095
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245703"
---
# <a name="creating-xaml-markup-extensions"></a>Creazione di estensioni di Markup XAML

Nel livello di programmazione, un'estensione di markup XAML è una classe che implementa il [ `IMarkupExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension/) o [ `IMarkupExtension<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.IMarkupExtension%3CT%3E/) interfaccia. È possibile esplorare il codice sorgente delle estensioni di markup standard descritti di seguito nel [ **MarkupExtensions** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del repository GitHub xamarin. Forms.

È anche possibile definire proprie estensioni di markup XAML personalizzate mediante derivazione da `IMarkupExtension` o `IMarkupExtension<T>`. Se l'estensione di markup Ottiene un valore di un determinato tipo, usare il modulo generico. Questo è il caso presenta molte delle estensioni di markup di xamarin. Forms:

- `TypeExtension` deriva da `IMarkupExtension<Type>`
- `ArrayExtension` deriva da `IMarkupExtension<Array>`
- `DynamicResourceExtension` deriva da `IMarkupExtension<DynamicResource>`
- `BindingExtension` deriva da `IMarkupExtension<BindingBase>`
- `ConstraintExpression` deriva da `IMarkupExtension<Constraint>`

I due `IMarkupExtension` le interfacce definiscono un solo metodo denominato `ProvideValue`:

```csharp
public interface IMarkupExtension
{
    object ProvideValue(IServiceProvider serviceProvider);
}

public interface IMarkupExtension<out T> : IMarkupExtension
{
    new T ProvideValue(IServiceProvider serviceProvider);
}
```

Poiché `IMarkupExtension<T>` deriva da `IMarkupExtension` e include il `new` la parola chiave nel `ProvideValue`, contiene sia `ProvideValue` metodi.

Le estensioni di markup XAML, molto spesso, definiscono le proprietà che contribuiscono al valore restituito. (L'unica eccezione è `NullExtension`, in cui `ProvideValue` restituisce semplicemente `null`.) Il `ProvideValue` metodo presenta un solo argomento di tipo `IServiceProvider` che verrà descritta più avanti in questo articolo.

## <a name="a-markup-extension-for-specifying-color"></a>Un'estensione di Markup per la specifica di colori

L'estensione di markup XAML seguente consente di costruire un `Color` valore utilizzando i componenti di tonalità, saturazione e luminosità. Definisce quattro proprietà per i quattro componenti di colore, tra cui un componente alfa viene inizializzato su 1. La classe deriva dalla `IMarkupExtension<Color>` per indicare un `Color` valore restituito:

```csharp
public class HslColorExtension : IMarkupExtension<Color>
{
    public double H { set; get; }

    public double S { set; get; }

    public double L { set; get; }

    public double A { set; get; } = 1.0;

    public Color ProvideValue(IServiceProvider serviceProvider)
    {
        return Color.FromHsla(H, S, L, A);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<Color>).ProvideValue(serviceProvider);
    }
}
```

Poiché `IMarkupExtension<T>` deriva da `IMarkupExtension`, la classe deve contenere due `ProvideValue` metodi, uno che restituisce `Color` e un altro che restituisce `object`, ma il secondo metodo può chiamare semplicemente il primo metodo.

Il **HSL colore Demo** pagina mostra vari modi `HslColorExtension` possono essere visualizzati in un file XAML per specificare il colore per un `BoxView`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.HslColorDemoPage"
             Title="HSL Color Demo">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="WidthRequest" Value="80" />
                <Setter Property="HeightRequest" Value="80" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <BoxView>
            <BoxView.Color>
                <local:HslColorExtension H="0" S="1" L="0.5" A="1" />
            </BoxView.Color>
        </BoxView>

        <BoxView>
            <BoxView.Color>
                <local:HslColor H="0.33" S="1" L="0.5" />
            </BoxView.Color>
        </BoxView>

        <BoxView Color="{local:HslColorExtension H=0.67, S=1, L=0.5}" />

        <BoxView Color="{local:HslColor H=0, S=0, L=0.5}" />

        <BoxView Color="{local:HslColor A=0.5}" />
    </StackLayout>
</ContentPage>
```

Si noti che quando `HslColorExtension` è un tag XML, le quattro proprietà vengono impostate come attributi, ma quando viene visualizzato tra parentesi graffe, le quattro proprietà sono separate da virgole senza virgolette. I valori predefiniti di `H`, `S`, e `L` sono 0 e il valore predefinito di `A` è 1, pertanto tali proprietà possono essere omesso se si desidera impostare valori predefiniti. Nell'ultimo esempio viene illustrato un esempio in cui la luminosità è 0, che comporta normalmente in nero, ma il canale alfa è 0,5, tale metà trasparente e viene visualizzato grigio contro lo sfondo della pagina:

[![Demo di colore HSL](creating-images/hslcolordemo-small.png "HSL colore Demo")](creating-images/hslcolordemo-large.png#lightbox "HSL colore Demo")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Un'estensione di Markup per l'accesso di bitmap

L'argomento `ProvideValue` è un oggetto che implementa il [ `IServiceProvider` ](https://developer.xamarin.com/api/type/System.IServiceProvider/) interfaccia, che è definito in .NET `System` dello spazio dei nomi. Questa interfaccia ha un membro, un metodo denominato `GetService` con un `Type` argomento.

Il `ImageResourceExtension` classe illustrato di seguito viene illustrato un possibile utilizzo di `IServiceProvider` e `GetService` per ottenere un `IXmlLineInfoProvider` oggetto che può fornire informazioni di riga e il carattere che indica dove è stato rilevato un errore specifico. In questo caso, viene generata un'eccezione quando il `Source` proprietà non è stata impostata:

```csharp
[ContentProperty("Source")]
class ImageResourceExtension : IMarkupExtension<ImageSource>
{
    public string Source { set; get; }

    public ImageSource ProvideValue(IServiceProvider serviceProvider)
    {
        if (String.IsNullOrEmpty(Source))
        {
            IXmlLineInfoProvider lineInfoProvider = serviceProvider.GetService(typeof(IXmlLineInfoProvider)) as IXmlLineInfoProvider;
            IXmlLineInfo lineInfo = (lineInfoProvider != null) ? lineInfoProvider.XmlLineInfo : new XmlLineInfo();
            throw new XamlParseException("ImageResourceExtension requires Source property to be set", lineInfo);
        }

        string assemblyName = GetType().GetTypeInfo().Assembly.GetName().Name;

        return ImageSource.FromResource(assemblyName + "." + Source);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension` è utile quando un file XAML deve accedere a un file di immagine archiviato come una risorsa incorporata nel progetto di libreria .NET Standard. Usa il `Source` proprietà per chiamare il metodo statico `ImageSource.FromResource` metodo. Questo metodo richiede un nome di risorsa completo che include il nome dell'assembly, il nome della cartella e il nome di file separati da punti. Il `ImageResourceExtension` non necessario nome dell'assembly parte perché Ottiene il nome dell'assembly tramite reflection e antepone al `Source` proprietà. Indipendentemente dal fatto che, `ImageSource.FromResource` deve essere chiamato dall'assembly che contiene la bitmap, il che significa che questa estensione di risorsa XAML non può essere parte di una libreria esterna, a meno che le immagini sono anche in tale raccolta. (Vedere il [ **immagini incorporate** ](~/xamarin-forms/user-interface/images.md#embedded_images) per ulteriori informazioni sull'accesso alle mappe di bit archiviati come risorse incorporate.)

Sebbene `ImageResourceExtension` richiede il `Source` proprietà viene impostata la `Source` proprietà è indicata in un attributo come proprietà di contenuto della classe. Ciò significa che il `Source=` parte dell'espressione tra parentesi graffe può essere omesse. Nel **Demo risorsa immagine** pagina il `Image` elementi recupero due immagini utilizzando il nome della cartella e il nome di file separati da punti:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.ImageResourceDemoPage"
             Title="Image Resource Demo">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image Source="{local:ImageResource Images.SeatedMonkey.jpg}"
               Grid.Row="0" />

        <Image Source="{local:ImageResource Images.FacePalm.jpg}"
               Grid.Row="1" />

    </Grid>
</ContentPage>
```

Di seguito è riportato il programma in esecuzione in tutti e tre le piattaforme:

[![Demo di risorsa immagine](creating-images/imageresourcedemo-small.png "immagine risorsa Demo")](creating-images/imageresourcedemo-large.png#lightbox "immagine Demo di risorse")

## <a name="service-providers"></a>Provider di servizi

Tramite il `IServiceProvider` argomento `ProvideValue`, le estensioni di markup XAML possono ottenere l'accesso a informazioni utili relative al file XAML in cui è in uso. Ma utilizzare il `IServiceProvider` argomento correttamente, è necessario conoscere il tipo di servizi sono disponibili in contesti particolari. Il modo migliore per ottenere le informazioni di questa funzionalità è esaminando il codice sorgente delle estensioni di markup XAML esistente nel [ **MarkupExtensions** cartella](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) nel repository in GitHub xamarin. Forms. Tenere presente che alcuni tipi di servizi sono interni a xamarin. Forms.

In alcune estensioni di markup XAML, il servizio potrebbe essere utile:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

Il `IProvideValueTarget` interfaccia definisce due proprietà, `TargetObject` e `TargetProperty`. Quando queste informazioni vengono ottenute nel `ImageResourceExtension` (classe), `TargetObject` è il `Image` e `TargetProperty` è un `BindableProperty` dell'oggetto per il `Source` proprietà `Image`. Questa è la proprietà in cui è stata impostata l'estensione di markup XAML.

Il `GetService` chiamata con un argomento di `typeof(IProvideValueTarget)` restituisce un oggetto di tipo `SimpleValueTargetProvider`, definito nel `Xamarin.Forms.Xaml.Internals` dello spazio dei nomi. Se si esegue il cast il valore restituito di `GetService` a tale tipo, è inoltre possibile accedere una `ParentObjects` proprietà, ovvero una matrice che contiene il `Image` elemento, il `Grid` padre e `ImageResourceDemoPage` padre del `Grid`.

## <a name="conclusion"></a>Conclusione

Le estensioni di markup XAML svolgono un ruolo fondamentale in XAML, estendendo la possibilità di impostare gli attributi da una varietà di origini. Inoltre, se le estensioni di markup XAML esistente non forniscono le informazioni desiderate, è possibile anche scrivere la propria.


## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capitolo di estensioni di markup XAML di libro xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
