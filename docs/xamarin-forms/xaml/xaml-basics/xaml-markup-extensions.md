---
title: Parte 3. Estensioni di markup XAML
description: Le estensioni di markup XAML costituiscono una funzionalità importante di XAML che consente di impostare le proprietà su oggetti o valori a cui viene fatto riferimento indirettamente da altre origini.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3fcea963b253ea34601a36434007f18d925975eb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131339"
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Estensioni di markup XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Le estensioni di markup XAML costituiscono una funzionalità importante di XAML che consente di impostare le proprietà su oggetti o valori a cui viene fatto riferimento indirettamente da altre origini. Le estensioni di markup XAML sono particolarmente importanti per la condivisione di oggetti e fanno riferimento a costanti utilizzate in un'applicazione, ma trovano l'utilità più grande nelle associazioni dati._

## <a name="xaml-markup-extensions"></a>Estensioni di markup XAML

In generale, si usa XAML per impostare le proprietà di un oggetto in modo esplicito, ad esempio una stringa, un numero, un membro di enumerazione o una stringa convertita in un valore dietro le quinte.

In alcuni casi, tuttavia, le proprietà devono fare riferimento a valori definiti altrove o che potrebbero richiedere un po' di elaborazione in base al codice in fase di esecuzione. A tale scopo, sono disponibili *estensioni di markup* XAML.

Queste estensioni di markup XAML non sono estensioni di XML. Il codice XAML è interamente valido. Sono denominate "estensioni" perché sono supportate dal codice nelle classi che implementano `IMarkupExtension` . È possibile scrivere estensioni di markup personalizzate.

In molti casi, le estensioni di markup XAML sono immediatamente riconoscibili nei file XAML perché vengono visualizzate come impostazioni degli attributi delimitate da parentesi graffe: {e}, ma a volte le estensioni di markup vengono visualizzate nel markup come elementi convenzionali.

## <a name="shared-resources"></a>Risorse condivise

Alcune pagine XAML contengono diverse visualizzazioni con proprietà impostate sugli stessi valori. Ad esempio, molte delle impostazioni delle proprietà per questi `Button` oggetti sono le stesse:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do that!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

        <Button Text="Do the other thing!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                BorderWidth="3"
                Rotation="-15"
                TextColor="Red"
                FontSize="24" />

    </StackLayout>
</ContentPage>
```

Se una di queste proprietà deve essere modificata, potrebbe essere preferibile apportare la modifica una sola volta anziché tre volte. Se si trattasse di codice, è probabile che si utilizzino costanti e oggetti statici di sola lettura per garantire la coerenza e la modifica di tali valori.

In XAML, una soluzione comune consiste nell'archiviare tali valori o oggetti in un *dizionario risorse*. La `VisualElement` classe definisce una proprietà denominata `Resources` di tipo `ResourceDictionary` , che è un dizionario con chiavi di tipo `string` e valori di tipo `object` . È possibile inserire oggetti in questo dizionario e quindi farvi riferimento dal markup, il tutto in XAML.

Per usare un dizionario risorse in una pagina, includere una coppia di `Resources` tag elemento proprietà. È consigliabile posizionarli nella parte superiore della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>

    </ContentPage.Resources>
    ...
</ContentPage>
```

È anche necessario includere in modo esplicito i `ResourceDictionary` Tag:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

È ora possibile aggiungere oggetti e valori di vari tipi al dizionario risorse. Questi tipi devono essere istanziabile. Non possono essere classi astratte, ad esempio. Questi tipi devono anche avere un costruttore pubblico senza parametri. Ogni elemento richiede una chiave del dizionario specificata con l' `x:Key` attributo. Ad esempio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Questi due elementi sono valori del tipo di struttura `LayoutOptions` e ognuno ha una chiave univoca e una o due proprietà impostate. Nel codice e nel markup, è molto più comune usare i campi statici di `LayoutOptions` , ma in questo caso è più pratico impostare le proprietà.

A questo punto è necessario impostare le `HorizontalOptions` `VerticalOptions` proprietà e di questi pulsanti su queste risorse. questa operazione viene eseguita con l' `StaticResource` estensione di markup XAML:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

L' `StaticResource` estensione di markup è sempre delimitata da parentesi graffe e include la chiave del dizionario.

Il nome lo `StaticResource` distingue da `DynamicResource` , che Xamarin.Forms supporta anche. `DynamicResource`è per chiavi del dizionario associate a valori che potrebbero cambiare in fase di esecuzione, mentre `StaticResource` accede agli elementi dal dizionario una sola volta quando vengono costruiti gli elementi nella pagina.

Per la `BorderWidth` proprietà, è necessario archiviare un valore Double nel dizionario. XAML definisce in modo appropriato i tag per i tipi di dati comuni, ad esempio `x:Double` e `x:Int32` :

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

        <x:Double x:Key="borderWidth">
            3
        </x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

Non è necessario inserirlo in tre righe. Questa voce del dizionario per l'angolo di rotazione occupa solo una riga:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <LayoutOptions x:Key="horzOptions"
                       Alignment="Center" />

        <LayoutOptions x:Key="vertOptions"
                       Alignment="Center"
                       Expands="True" />

         <x:Double x:Key="borderWidth">
            3
         </x:Double>

        <x:Double x:Key="rotationAngle">-15</x:Double>
    </ResourceDictionary>
</ContentPage.Resources>
```

È possibile fare riferimento a queste due risorse nello stesso modo dei `LayoutOptions` valori:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Per le risorse di tipo `Color` , è possibile usare le stesse rappresentazioni di stringa usate quando si assegnano direttamente attributi di questi tipi. I convertitori di tipi vengono richiamati quando viene creata la risorsa. Ecco una risorsa di tipo `Color` :

```xaml
<Color x:Key="textColor">Red</Color>
```

Spesso, i programmi impostano una `FontSize` proprietà su un membro dell' `NamedSize` enumerazione, ad esempio `Large` . La `FontSizeConverter` classe funziona dietro le quinte per convertirla in un valore dipendente dalla piattaforma usando `Device.GetNamedSized` il metodo. Tuttavia, quando si definisce una risorsa di dimensioni del tipo di carattere, è più sensato usare un valore numerico, riportato di seguito come `x:Double` tipo:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Ora tutte le proprietà eccetto `Text` sono definite dalle impostazioni delle risorse:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

È anche possibile usare `OnPlatform` all'interno del dizionario risorse per definire valori diversi per le piattaforme. Di seguito viene illustrato `OnPlatform` il modo in cui un oggetto può far parte del dizionario risorse per i diversi colori del testo:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Si noti che `OnPlatform` ottiene un `x:Key` attributo perché è un oggetto nel dizionario e un `x:TypeArguments` attributo perché si tratta di una classe generica. Gli `iOS` `Android` attributi, e `UWP` vengono convertiti in `Color` valori quando l'oggetto viene inizializzato.

Ecco il file XAML completo finale con tre pulsanti che accedono a sei valori condivisi:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SharedResourcesPage"
             Title="Shared Resources Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <LayoutOptions x:Key="horzOptions"
                           Alignment="Center" />

            <LayoutOptions x:Key="vertOptions"
                           Alignment="Center"
                           Expands="True" />

            <x:Double x:Key="borderWidth">3</x:Double>

            <x:Double x:Key="rotationAngle">-15</x:Double>

            <OnPlatform x:Key="textColor"
                        x:TypeArguments="Color">
                <On Platform="iOS" Value="Red" />
                <On Platform="Android" Value="Aqua" />
                <On Platform="UWP" Value="#80FF80" />
            </OnPlatform>

            <x:Double x:Key="fontSize">24</x:Double>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <Button Text="Do this!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do that!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

        <Button Text="Do the other thing!"
                HorizontalOptions="{StaticResource horzOptions}"
                VerticalOptions="{StaticResource vertOptions}"
                BorderWidth="{StaticResource borderWidth}"
                Rotation="{StaticResource rotationAngle}"
                TextColor="{StaticResource textColor}"
                FontSize="{StaticResource fontSize}" />

    </StackLayout>
</ContentPage>
```

Gli screenshot verificano lo stile coerente e lo stile dipendente dalla piattaforma:

[![Controlli con stile](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

Sebbene sia più comune definire la `Resources` raccolta nella parte superiore della pagina, tenere presente che la `Resources` proprietà è definita da `VisualElement` ed è possibile avere `Resources` raccolte in altri elementi della pagina. Ad esempio, provare ad aggiungere uno a `StackLayout` in questo esempio:

```xaml
<StackLayout>
    <StackLayout.Resources>
        <ResourceDictionary>
            <Color x:Key="textColor">Blue</Color>
        </ResourceDictionary>
    </StackLayout.Resources>
    ...
</StackLayout>
```

Si scoprirà che il colore del testo dei pulsanti è ora blu. In pratica, ogni volta che il parser XAML rileva un' `StaticResource` estensione di markup, esegue la ricerca nella struttura ad albero visuale e usa il primo `ResourceDictionary` rilevato che contiene tale chiave.

Uno dei tipi più comuni di oggetti archiviati nei dizionari risorse è Xamarin.Forms `Style` , che definisce una raccolta di impostazioni delle proprietà. Gli stili vengono descritti negli [stili](~/xamarin-forms/user-interface/styles/index.md)degli articoli.

A volte gli sviluppatori nuovi a XAML si chiedono se possono inserire un elemento visivo, ad esempio `Label` o, `Button` in un `ResourceDictionary` . Sebbene sia certamente possibile, non ha molto senso. Lo scopo di `ResourceDictionary` è quello di condividere gli oggetti. Non è possibile condividere un elemento visivo. La stessa istanza non può comparire due volte in una singola pagina.

## <a name="the-xstatic-markup-extension"></a>Estensione di markup x:Static

Nonostante le analogie dei nomi, `x:Static` e `StaticResource` sono molto diversi. `StaticResource`Restituisce un oggetto da un dizionario risorse mentre `x:Static` accede a uno dei seguenti elementi:

- campo statico pubblico
- una proprietà statica pubblica
- campo costante pubblico
- membro di enumerazione.

L' `StaticResource` estensione di markup è supportata dalle implementazioni XAML che definiscono un dizionario risorse, mentre `x:Static` è una parte intrinseca di XAML, come il `x` prefisso rivela.

Ecco alcuni esempi che illustrano in che modo è `x:Static` possibile fare riferimento in modo esplicito ai campi statici e ai membri di enumerazione:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Fino a questo punto non è molto impressionante. Tuttavia `x:Static` , l'estensione di markup può anche fare riferimento a campi o proprietà statiche dal proprio codice. Ecco, ad esempio, una `AppConstants` classe che contiene alcuni campi statici che possono essere usati in più pagine in un'applicazione:

```csharp
using System;
using Xamarin.Forms;

namespace XamlSamples
{
    static class AppConstants
    {
        public static readonly Thickness PagePadding;

        public static readonly Font TitleFont;

        public static readonly Color BackgroundColor = Color.Aqua;

        public static readonly Color ForegroundColor = Color.Brown;

        static AppConstants()
        {
            switch (Device.RuntimePlatform)
            {
                case Device.iOS:
                    PagePadding = new Thickness(5, 20, 5, 0);
                    TitleFont = Font.SystemFontOfSize(35, FontAttributes.Bold);
                    break;

                case Device.Android:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(40, FontAttributes.Bold);
                    break;

                case Device.UWP:
                    PagePadding = new Thickness(5, 0, 5, 0);
                    TitleFont = Font.SystemFontOfSize(50, FontAttributes.Bold);
                    break;
            }
        }
    }
}
```

Per fare riferimento ai campi statici di questa classe nel file XAML, è necessario un modo per indicare all'interno del file XAML in cui si trova il file. Questa operazione viene eseguita con una dichiarazione dello spazio dei nomi XML.

Si ricordi che i file XAML creati come parte del Xamarin.Forms modello XAML standard contengono due dichiarazioni dello spazio dei nomi XML: una per l'accesso alle Xamarin.Forms classi e un'altra per fare riferimento a tag e attributi intrinseci a XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

Per accedere ad altre classi sono necessarie altre dichiarazioni dello spazio dei nomi XML. Ogni dichiarazione dello spazio dei nomi XML aggiuntiva definisce un nuovo prefisso. Per accedere alle classi locali per l'applicazione condivisa .NET Standard libreria, ad esempio `AppConstants` , i programmatori XAML usano spesso il prefisso `local` . La dichiarazione dello spazio dei nomi deve indicare il nome dello spazio dei nomi CLR (Common Language Runtime), noto anche come nome dello spazio dei nomi .NET, che è il nome che viene visualizzato in una `namespace` definizione C# o in una `using` direttiva:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

È anche possibile definire dichiarazioni di spazi dei nomi XML per gli spazi dei nomi .NET in qualsiasi assembly a cui fa riferimento la libreria .NET Standard. Ad esempio, di seguito è riportato un `sys` prefisso per lo `System` spazio dei nomi .NET standard, disponibile nell'assembly **netstandard** . Poiché si tratta di un altro assembly, è necessario specificare anche il nome dell'assembly, in questo caso **netstandard**:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Si noti che la parola chiave `clr-namespace` è seguita da due punti, quindi dal nome dello spazio dei nomi .NET, seguito da un punto e virgola, dalla parola chiave, da un `assembly` segno di uguale e dal nome dell'assembly.

Sì, seguono due punti, `clr-namespace` ma il segno di uguale `assembly` . La sintassi è stata definita in questo modo intenzionalmente: la maggior parte delle dichiarazioni dello spazio dei nomi XML fa riferimento a un URI che inizia con un nome di schema URI, ad esempio `http` , che è sempre seguito da due punti. La `clr-namespace` parte di questa stringa è progettata per simulare tale convenzione.

Entrambe le dichiarazioni dello spazio dei nomi sono incluse nell'esempio **StaticConstantsPage** . Si noti che le `BoxView` dimensioni sono impostate su `Math.PI` e `Math.E` , ma ridimensionate in base a un fattore di 100:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="XamlSamples.StaticConstantsPage"
             Title="Static Constants Page"
             Padding="{x:Static local:AppConstants.PagePadding}">

    <StackLayout>
       <Label Text="Hello, XAML!"
              TextColor="{x:Static local:AppConstants.BackgroundColor}"
              BackgroundColor="{x:Static local:AppConstants.ForegroundColor}"
              Font="{x:Static local:AppConstants.TitleFont}"
              HorizontalOptions="Center" />

      <BoxView WidthRequest="{x:Static sys:Math.PI}"
               HeightRequest="{x:Static sys:Math.E}"
               Color="{x:Static local:AppConstants.ForegroundColor}"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand"
               Scale="100" />
    </StackLayout>
</ContentPage>
```

Le dimensioni del risultato `BoxView` relativo allo schermo sono dipendenti dalla piattaforma:

[![Controlli che usano l'estensione di markup x:Static](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>Altre estensioni di markup standard

Diverse estensioni di markup sono intrinseche a XAML e supportate nei Xamarin.Forms file XAML. Alcuni di questi non vengono utilizzati molto spesso, ma sono essenziali quando sono necessari:

- Se una proprietà ha un valore non `null` valore per impostazione predefinita, ma si vuole impostarla su `null` , impostarla sull' `{x:Null}` estensione di markup.
- Se una proprietà è di tipo `Type` , è possibile assegnarla a un `Type` oggetto usando l'estensione di markup `{x:Type someClass}` .
- È possibile definire matrici in XAML usando l' `x:Array` estensione di markup. Questa estensione di markup dispone di un attributo obbligatorio denominato `Type` che indica il tipo degli elementi nella matrice.
- L' `Binding` estensione di markup è illustrata nella [parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).
- L' `RelativeSource` estensione di markup viene descritta in [associazioni relative](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="the-constraintexpression-markup-extension"></a>Estensione di markup ConstraintExpression

Le estensioni di markup possono avere proprietà, ma non sono impostate come attributi XML. In un'estensione di markup le impostazioni delle proprietà sono separate da virgole e nessuna virgoletta viene visualizzata tra parentesi graffe.

Questa operazione può essere illustrata con l' Xamarin.Forms estensione di markup denominata `ConstraintExpression` , che viene usata con la `RelativeLayout` classe. È possibile specificare la posizione o le dimensioni di una visualizzazione figlio come costante o relativa a un elemento padre o a un'altra visualizzazione denominata. La sintassi di `ConstraintExpression` consente di impostare la posizione o le dimensioni di una visualizzazione utilizzando un oggetto `Factor` volte una proprietà di un'altra visualizzazione, più un oggetto `Constant` . Qualsiasi elemento più complesso di quello che richiede codice.

Ecco un esempio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.RelativeLayoutPage"
             Title="RelativeLayout Page">

    <RelativeLayout>

        <!-- Upper left -->
        <BoxView Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Upper right -->
        <BoxView Color="Green"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}" />
        <!-- Lower left -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=Constant,
                                            Constant=0}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />
        <!-- Lower right -->
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=1,
                                            Constant=-40}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=1,
                                            Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Red"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToParent,
                                            Property=Height,
                                            Factor=0.33}"  />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=X}"
                 RelativeLayout.YConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Y}"
                 RelativeLayout.WidthConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Width,
                                            Factor=0.33}"
                 RelativeLayout.HeightConstraint=
                     "{ConstraintExpression Type=RelativeToView,
                                            ElementName=oneThird,
                                            Property=Height,
                                            Factor=0.33}"  />
    </RelativeLayout>
</ContentPage>
```

Forse la lezione più importante che è opportuno prendere da questo esempio è la sintassi dell'estensione di markup: non è necessario che le virgolette siano incluse tra parentesi graffe di un'estensione di markup. Quando si digita l'estensione di markup in un file XAML, è naturale voler racchiudere tra virgolette i valori delle proprietà. Resistere alla tentazione!

Ecco il programma che esegue:

[![Layout relativo con vincoli](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>Summary

Le estensioni di markup XAML illustrate di seguito forniscono un importante supporto per i file XAML. Ma forse l'estensione di markup XAML più preziosa è `Binding` , che verrà trattata nella parte successiva della serie, [parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introduzione con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Dal data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
