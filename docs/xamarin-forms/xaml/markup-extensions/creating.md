---
title: Creazione di estensioni di Markup XAML
description: Questo articolo illustra come definire delle estensioni di markup XAML di xamarin. Forms personalizzate. Un'estensione di markup XAML è una classe che implementa l'interfaccia IMarkupExtension IMarkupExtension.
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: b928c55f447d68b8adfedaa031fd85750ee71267
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563706"
---
# <a name="creating-xaml-markup-extensions"></a>Creazione di estensioni di Markup XAML

Il livello a livello di codice, un'estensione di markup XAML è una classe che implementa il [ `IMarkupExtension` ](xref:Xamarin.Forms.Xaml.IMarkupExtension) oppure [ `IMarkupExtension<T>` ](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) interfaccia. È possibile esplorare il codice sorgente delle estensioni di markup standard descritto di seguito nel [ **MarkupExtensions** directory](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del repository GitHub xamarin. Forms.

È anche possibile definire le proprie estensioni di markup XAML personalizzate mediante derivazione dalla `IMarkupExtension` o `IMarkupExtension<T>`. Se l'estensione di markup Ottiene un valore di un determinato tipo, usare il modulo generico. Questo è il caso di alcuni fra le estensioni di markup di xamarin. Forms:

- `TypeExtension` deriva da `IMarkupExtension<Type>`
- `ArrayExtension` deriva da `IMarkupExtension<Array>`
- `DynamicResourceExtension` deriva da `IMarkupExtension<DynamicResource>`
- `BindingExtension` deriva da `IMarkupExtension<BindingBase>`
- `ConstraintExpression` deriva da `IMarkupExtension<Constraint>`

I due `IMarkupExtension` interfacce definiscono un solo metodo denominato `ProvideValue`:

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

Molto spesso, le estensioni di markup XAML definiscono le proprietà che contribuiscono al valore restituito. (L'unica eccezione è costituita `NullExtension`, in cui `ProvideValue` restituisce semplicemente `null`.) Il `ProvideValue` metodo presenta un solo argomento di tipo `IServiceProvider` che verrà descritta più avanti in questo articolo.

## <a name="a-markup-extension-for-specifying-color"></a>Un'estensione di Markup per la specifica di colori

L'estensione di markup XAML seguente consente di costruire un `Color` valore usando i componenti di tonalità, saturazione e luminosità. Definisce le quattro proprietà per i quattro componenti del colore, tra cui un componente alfa che viene inizializzato su 1. La classe deriva da `IMarkupExtension<Color>` per indicare un `Color` valore restituito:

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

In quanto `IMarkupExtension<T>` deriva da `IMarkupExtension`, la classe deve contenere due `ProvideValue` metodi, che restituisce `Color` e l'altro che restituisce `object`, mentre il secondo metodo può chiamare semplicemente il primo metodo.

Il **Demo di colori HSL** pagina mostra vari modi `HslColorExtension` possono essere visualizzati in un file XAML per specificare il colore per un `BoxView`:

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

Si noti che quando `HslColorExtension` è un tag XML, le quattro proprietà vengono impostate come attributi, ma quando viene visualizzato tra parentesi graffe, le quattro proprietà sono separate da virgole senza virgolette. I valori predefiniti per `H`, `S`, e `L` sono 0 e il valore predefinito di `A` è 1, in modo che tali proprietà possono essere omesso se si vuole che siano impostate sui valori predefiniti. Nell'ultimo esempio viene illustrato un esempio in cui la luminosità è 0, che in genere corrisponde al nero, ma il canale alfa è 0,5, tale è metà trasparente e viene visualizzato grigia sullo sfondo bianco della pagina:

[![Demo di colori HSL](creating-images/hslcolordemo-small.png "Demo di colori HSL")](creating-images/hslcolordemo-large.png#lightbox "Demo di colori HSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Un'estensione di Markup per l'accesso alle bitmap

L'argomento `ProvideValue` è un oggetto che implementa le [ `IServiceProvider` ](xref:System.IServiceProvider) interfaccia, che viene definito in .NET `System` dello spazio dei nomi. Questa interfaccia dispone di un membro, un metodo denominato `GetService` con un `Type` argomento.

Il `ImageResourceExtension` classe illustrato di seguito viene illustrato un possibile utilizzo dei `IServiceProvider` e `GetService` per ottenere un `IXmlLineInfoProvider` oggetti che possono fornire informazioni di riga e il carattere che indica dove è stato rilevato un errore specifico. In questo caso, viene generata un'eccezione quando il `Source` proprietà non è stata impostata:

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

`ImageResourceExtension` è utile quando un file XAML deve accedere a un file di immagine archiviato come una risorsa incorporata nel progetto della libreria .NET Standard. Usa il `Source` proprietà per chiamare il metodo statico `ImageSource.FromResource` (metodo). Questo metodo richiede un nome di risorsa completo, che include il nome dell'assembly, il nome della cartella e il nome del file separati da punti. Il `ImageResourceExtension` non necessari nome dell'assembly parte perché Ottiene il nome dell'assembly tramite reflection e antepone al `Source` proprietà. Indipendentemente dal fatto che, `ImageSource.FromResource` deve essere chiamato dall'assembly che contiene la bitmap, il che significa che questa estensione di risorsa XAML può far parte di una libreria esterna a meno che le immagini sono anche in tale raccolta. (Vedere la [ **immagini incorporate** ](~/xamarin-forms/user-interface/images.md#embedded-images) per altre informazioni sull'accesso alle bitmap archiviata come risorse incorporate.)

Sebbene `ImageResourceExtension` richiede la `Source` proprietà viene impostata, il `Source` proprietà è indicata in un attributo come proprietà di contenuto della classe. Ciò significa che il `Source=` parte dell'espressione tra parentesi graffe può essere omessi. Nel **Demo risorsa immagine** pagina il `Image` elementi recupero due immagini usando il nome della cartella e il nome del file separati da punti:

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

Ecco il programma in esecuzione in tutti e tre le piattaforme:

[![Demo di risorsa di immagine](creating-images/imageresourcedemo-small.png "immagine risorse Demo")](creating-images/imageresourcedemo-large.png#lightbox "Demo di risorsa di immagine")

## <a name="service-providers"></a>Provider di servizi

Tramite il `IServiceProvider` argomento per `ProvideValue`, estensioni di markup XAML è possono accedere a informazioni utili relative al file XAML in cui vengono usati. Ma per usare il `IServiceProvider` argomento correttamente, è necessario conoscere quali tipi di servizi sono disponibili in contesti specifici. Il modo migliore per ottenere le informazioni di questa funzionalità è esaminando il codice sorgente delle estensioni di markup XAML esistente nel [ **MarkupExtensions** cartella](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) nel repository di xamarin. Forms in GitHub. Tenere presente che alcuni tipi di servizi sono interni a xamarin. Forms.

In alcune estensioni di markup XAML, questo servizio potrebbe essere utile:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

Il `IProvideValueTarget` interfaccia definisce due proprietà, `TargetObject` e `TargetProperty`. Quando queste informazioni vengono ottenute nel `ImageResourceExtension` (classe), `TargetObject` è la `Image` e `TargetProperty` è un `BindableProperty` dell'oggetto per i `Source` proprietà di `Image`. Si tratta della proprietà in cui è stata impostata l'estensione di markup XAML.

Il `GetService` chiamata con un argomento di `typeof(IProvideValueTarget)` effettivamente restituisce un oggetto di tipo `SimpleValueTargetProvider`, definito nel `Xamarin.Forms.Xaml.Internals` dello spazio dei nomi. Se si esegue il cast il valore restituito di `GetService` per quel tipo, è anche possibile accedere una `ParentObjects` proprietà, ovvero una matrice che contiene il `Image` elemento, il `Grid` padre e il `ImageResourceDemoPage` padre del `Grid`.

## <a name="conclusion"></a>Conclusione

Le estensioni di markup XAML svolgono un ruolo fondamentale in XAML mediante l'estensione la possibilità di impostare gli attributi provenienti da numerose origini. Inoltre, se le estensioni di markup XAML esistente non forniscono le informazioni desiderate, è possibile anche scrivere il proprio.


## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capitolo di estensioni di markup XAML dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
