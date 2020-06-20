---
title: Creazione di estensioni di markup XAML
description: Questo articolo illustra come definire Xamarin.Forms estensioni di markup XAML personalizzate. Un'estensione di markup XAML è una classe che implementa l'interfaccia IMarkupExtension o IMarkupExtension <T> .
ms.prod: xamarin
ms.assetid: 797C1EF9-1C8E-4208-8610-9B79CCF17D46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 878ebcaa5249261afac2776a9e7cf47c0c047135
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84130533"
---
# <a name="creating-xaml-markup-extensions"></a>Creazione di estensioni di markup XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

A livello di codice, un'estensione di markup XAML è una classe che implementa [`IMarkupExtension`](xref:Xamarin.Forms.Xaml.IMarkupExtension) l' [`IMarkupExtension<T>`](xref:Xamarin.Forms.Xaml.IMarkupExtension`1) interfaccia o. È possibile esplorare il codice sorgente delle estensioni di markup standard descritte di seguito nella [directory **MarkupExtensions** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del Xamarin.Forms repository GitHub.

È anche possibile definire estensioni di markup XAML personalizzate derivando da `IMarkupExtension` o `IMarkupExtension<T>` . Usare il formato generico se l'estensione di markup ottiene un valore di un tipo specifico. Questo è il caso con diverse estensioni di Xamarin.Forms markup:

- `TypeExtension`deriva da`IMarkupExtension<Type>`
- `ArrayExtension`deriva da`IMarkupExtension<Array>`
- `DynamicResourceExtension`deriva da`IMarkupExtension<DynamicResource>`
- `BindingExtension`deriva da`IMarkupExtension<BindingBase>`
- `ConstraintExpression`deriva da`IMarkupExtension<Constraint>`

Le due `IMarkupExtension` interfacce definiscono un solo metodo, denominato `ProvideValue` :

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

Poiché `IMarkupExtension<T>` deriva da `IMarkupExtension` e include la `new` parola chiave in `ProvideValue` , contiene entrambi i `ProvideValue` metodi.

Molto spesso, le estensioni di markup XAML definiscono proprietà che contribuiscono al valore restituito. L'eccezione ovvia è `NullExtension` , in quanto `ProvideValue` restituisce semplicemente `null` . Il `ProvideValue` metodo ha un solo argomento di tipo `IServiceProvider` che verrà illustrato più avanti in questo articolo.

## <a name="a-markup-extension-for-specifying-color"></a>Estensione di markup per specificare il colore

L'estensione di markup XAML seguente consente di costruire un `Color` valore usando i componenti tonalità, saturazione e luminosità. Definisce quattro proprietà per i quattro componenti del colore, incluso un componente alfa inizializzato su 1. La classe deriva da `IMarkupExtension<Color>` per indicare un `Color` valore restituito:

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

Poiché `IMarkupExtension<T>` deriva da `IMarkupExtension` , la classe deve contenere due `ProvideValue` metodi, uno che restituisce `Color` e un altro che restituisce `object` , ma il secondo metodo può semplicemente chiamare il primo metodo.

La pagina **demo del colore HSL** Mostra diversi modi che `HslColorExtension` possono essere visualizzati in un file XAML per specificare il colore per un `BoxView` :

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

Si noti che quando `HslColorExtension` è un tag XML, le quattro proprietà vengono impostate come attributi, ma quando vengono visualizzate tra parentesi graffe, le quattro proprietà sono separate da virgole senza virgolette. I valori predefiniti per `H` , `S` e `L` sono 0 e il valore predefinito di `A` è 1, pertanto tali proprietà possono essere omesse se si desidera che vengano impostate sui valori predefiniti. Nell'ultimo esempio viene illustrato un esempio in cui la luminosità è 0, che in genere produce il nero, ma il canale alfa è 0,5, quindi è la metà trasparente e viene visualizzato grigio rispetto allo sfondo bianco della pagina:

[![Demo colore HSL](creating-images/hslcolordemo-small.png "Demo colore HSL")](creating-images/hslcolordemo-large.png#lightbox "Demo colore HSL")

## <a name="a-markup-extension-for-accessing-bitmaps"></a>Estensione di markup per l'accesso alle bitmap

L'argomento per `ProvideValue` è un oggetto che implementa l' [`IServiceProvider`](xref:System.IServiceProvider) interfaccia, definita nello `System` spazio dei nomi .NET. Questa interfaccia dispone di un membro, un metodo denominato `GetService` con un `Type` argomento.

La `ImageResourceExtension` classe riportata di seguito mostra un possibile uso di `IServiceProvider` e `GetService` per ottenere un `IXmlLineInfoProvider` oggetto che può fornire informazioni sulla riga e sui caratteri che indicano la posizione in cui è stato rilevato un errore specifico. In questo caso, viene generata un'eccezione quando la `Source` proprietà non è stata impostata:

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
        return ImageSource.FromResource(assemblyName + "." + Source, typeof(ImageResourceExtension).GetTypeInfo().Assembly);
    }

    object IMarkupExtension.ProvideValue(IServiceProvider serviceProvider)
    {
        return (this as IMarkupExtension<ImageSource>).ProvideValue(serviceProvider);
    }
}
```

`ImageResourceExtension`è utile quando un file XAML deve accedere a un file di immagine archiviato come risorsa incorporata nel progetto di libreria .NET Standard. Usa la `Source` proprietà per chiamare il metodo statico `ImageSource.FromResource` . Questo metodo richiede un nome di risorsa completo, costituito dal nome dell'assembly, dal nome della cartella e dal nome file separati da punti. Il secondo argomento del `ImageSource.FromResource` metodo fornisce il nome dell'assembly ed è necessario solo per le build di versione in UWP. Indipendentemente `ImageSource.FromResource` da, deve essere chiamato dall'assembly che contiene la bitmap, il che significa che questa estensione di risorsa XAML non può far parte di una libreria esterna a meno che anche le immagini non si trovino in tale libreria. Per ulteriori informazioni sull'accesso alle bitmap archiviate come risorse incorporate, vedere l'articolo [**Immagini incorporate**](~/xamarin-forms/user-interface/images.md#embedded-images) .

Sebbene sia `ImageResourceExtension` necessario `Source` impostare la proprietà, la `Source` proprietà viene indicata in un attributo come proprietà di contenuto della classe. Ciò significa che è `Source=` possibile omettere la parte dell'espressione racchiusa tra parentesi graffe. Nella pagina **demo delle risorse immagine** gli `Image` elementi recuperano due immagini usando il nome della cartella e il nome file separati da punti:

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

Ecco il programma in esecuzione:

[![Demo sulle risorse immagine](creating-images/imageresourcedemo-small.png "Demo sulle risorse immagine")](creating-images/imageresourcedemo-large.png#lightbox "Demo sulle risorse immagine")

## <a name="service-providers"></a>Provider di servizi

Utilizzando l' `IServiceProvider` argomento a `ProvideValue` , le estensioni di markup XAML possono accedere a informazioni utili sul file XAML in cui vengono utilizzate. Tuttavia, per utilizzare `IServiceProvider` correttamente l'argomento, è necessario conoscere il tipo di servizi disponibili in contesti specifici. Il modo migliore per conoscere questa funzionalità consiste nel studiare il codice sorgente delle estensioni di markup XAML esistenti nella [cartella **MarkupExtensions** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Xaml/MarkupExtensions) del Xamarin.Forms repository su GitHub. Tenere presente che alcuni tipi di servizi sono interni a Xamarin.Forms .

In alcune estensioni di markup XAML, questo servizio può essere utile:

```csharp
 IProvideValueTarget provideValueTarget = serviceProvider.GetService(typeof(IProvideValueTarget)) as IProvideValueTarget;
```

L' `IProvideValueTarget` interfaccia definisce due proprietà, `TargetObject` e `TargetProperty` . Quando queste informazioni vengono ottenute nella `ImageResourceExtension` classe, `TargetObject` è `Image` e `TargetProperty` è un `BindableProperty` oggetto per la `Source` proprietà di `Image` . Si tratta della proprietà in cui è stata impostata l'estensione di markup XAML.

La `GetService` chiamata con un argomento di `typeof(IProvideValueTarget)` restituisce effettivamente un oggetto di tipo `SimpleValueTargetProvider` , definito nello `Xamarin.Forms.Xaml.Internals` spazio dei nomi. Se si esegue il cast del valore restituito di `GetService` a quel tipo, è anche possibile accedere a una `ParentObjects` proprietà, che è una matrice che contiene l' `Image` elemento, l'elemento `Grid` padre e l'elemento `ImageResourceDemoPage` padre di `Grid` .

## <a name="conclusion"></a>Conclusioni

Le estensioni di markup XAML svolgono un ruolo fondamentale in XAML estendendo la possibilità di impostare attributi da un'ampia gamma di origini. Inoltre, se le estensioni di markup XAML esistenti non forniscono esattamente ciò che è necessario, è anche possibile scriverne di personalizzate.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capitolo del libro sulle estensioni di markup XAML Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
