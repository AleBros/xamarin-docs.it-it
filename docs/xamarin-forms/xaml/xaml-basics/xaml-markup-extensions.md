---
title: Parte 3. Estensioni di markup XAML
description: Le estensioni di markup XAML costituiscano un'importante funzionalità in XAML che consentono di proprietà da impostare per gli oggetti o valori di cui vengono fatto riferimento indirettamente da altre origini.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: davidbritch
ms.author: dabritch
ms.date: 03/27/2018
ms.openlocfilehash: 89e2026ff16a9614234d6ee4bfa4df620cf58b56
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304457"
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Estensioni di markup XAML

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Le estensioni di markup XAML costituiscono una funzionalità importante di XAML che consente di impostare le proprietà su oggetti o valori a cui viene fatto riferimento indirettamente da altre origini. Le estensioni di markup XAML sono particolarmente importanti per la condivisione di oggetti e fanno riferimento a costanti utilizzate in un'applicazione, ma trovano l'utilità più grande nelle associazioni dati._

## <a name="xaml-markup-extensions"></a>Estensioni di markup XAML

In generale, è utilizzare XAML per impostare le proprietà di un oggetto valori espliciti, ad esempio una stringa, un numero, un membro di enumerazione o una stringa che viene convertita in un valore dietro le quinte.

In alcuni casi, tuttavia, proprietà devono invece fare riferimento a valori definiti da qualche parte else o che potrebbero richiedere un poche operazioni di elaborazione mediante codice in fase di esecuzione. A tale scopo, sono disponibili *estensioni di markup* XAML.

Queste estensioni di markup XAML non sono le estensioni del linguaggio XML. XAML è interamente legali XML. Sono denominate "estensioni" perché sono supportate dal codice nelle classi che implementano `IMarkupExtension`. È possibile scrivere le proprie estensioni di markup personalizzata.

In molti casi, le estensioni di markup XAML vengono immediatamente riconoscibile nei file XAML perché appaiono come impostazioni di attributo delimitate da parentesi graffe: {e}, ma in alcuni casi le estensioni di markup viene visualizzato nel markup come elementi convenzionali.

## <a name="shared-resources"></a>Risorse condivise

Alcune pagine XAML contengono visualizzazioni diverse con le proprietà impostate per gli stessi valori. Ad esempio, molte delle impostazioni delle proprietà per questi oggetti `Button` sono le stesse:

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

Se una di queste proprietà deve essere modificato, è preferibile apportare la modifica di una sola volta anziché tre volte. Se si trattasse di codice, viene probabilmente usato costanti e gli oggetti statici di sola lettura per mantenere tali valori, coerente e facile da modificare.

In XAML, una soluzione comune consiste nell'archiviare tali valori o oggetti in un *dizionario risorse*. La classe `VisualElement` definisce una proprietà denominata `Resources` di tipo `ResourceDictionary`, ovvero un dizionario con chiavi di tipo `string` e valori di tipo `object`. È possibile inserire oggetti in questo dizionario e quindi farvi riferimento dal markup, tutto in XAML.

Per usare un dizionario risorse in una pagina, includere una coppia di `Resources` tag elemento proprietà. È più pratico inserire gli elementi nella parte superiore della pagina:

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

È anche necessario includere in modo esplicito `ResourceDictionary` Tag:

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

Ora gli oggetti e i valori di vari tipi possono essere aggiunti a dizionario risorse. Questi tipi devono essere istanziabili. Non possono essere classi astratte, ad esempio. Questi tipi devono anche avere un costruttore pubblico senza parametri. Ogni elemento richiede una chiave del dizionario specificata con l'attributo `x:Key`. Ad esempio,

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

Questi due elementi sono valori del tipo di struttura `LayoutOptions`e ognuno ha una chiave univoca e una o due proprietà impostate. Nel codice e nel markup, è molto più comune usare i campi statici di `LayoutOptions`, ma in questo caso è più pratico impostare le proprietà.

A questo punto è necessario impostare le proprietà `HorizontalOptions` e `VerticalOptions` di questi pulsanti su queste risorse. questa operazione viene eseguita con l'estensione di markup XAML `StaticResource`:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

Il `StaticResource` estensione di markup è sempre delimitato da parentesi graffe e include la chiave del dizionario.

Il nome `StaticResource` lo distingue da `DynamicResource`, supportato anche da Novell. Forms. `DynamicResource` è per chiavi del dizionario associate a valori che potrebbero cambiare in fase di esecuzione, mentre `StaticResource` accede agli elementi dal dizionario una sola volta quando vengono costruiti gli elementi nella pagina.

Per la proprietà `BorderWidth`, è necessario archiviare un valore Double nel dizionario. XAML definisce in modo appropriato i tag per i tipi di dati comuni, ad esempio `x:Double` e `x:Int32`:

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

Non è necessario inserirlo nelle tre righe. Questa voce del dizionario per l'angolo di rotazione richiede solo una riga in alto:

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

È possibile fare riferimento a queste due risorse nello stesso modo dei valori `LayoutOptions`:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Per le risorse di tipo `Color`, è possibile usare le stesse rappresentazioni di stringa usate quando si assegnano direttamente attributi di questi tipi. Quando viene creata la risorsa, vengono richiamati i convertitori di tipi. Ecco una risorsa di tipo `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Spesso, i programmi impostano una proprietà `FontSize` a un membro dell'enumerazione `NamedSize`, ad esempio `Large`. La classe `FontSizeConverter` funziona dietro le quinte per convertirla in un valore dipendente dalla piattaforma usando il metodo `Device.GetNamedSized`. Tuttavia, quando si definisce una risorsa di dimensioni del tipo di carattere, è più opportuno usare un valore numerico, illustrato di seguito come tipo di `x:Double`:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

Tutte le proprietà eccetto `Text` sono ora definite dalle impostazioni delle risorse:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

È anche possibile usare `OnPlatform` all'interno del dizionario risorse per definire valori diversi per le piattaforme. Di seguito viene illustrato il modo in cui un oggetto `OnPlatform` può far parte del dizionario risorse per diversi colori del testo:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Si noti che `OnPlatform` ottiene un attributo `x:Key` perché è un oggetto nel dizionario e un attributo `x:TypeArguments` perché si tratta di una classe generica. Gli attributi `iOS`, `Android`e `UWP` vengono convertiti in valori `Color` quando l'oggetto viene inizializzato.

Ecco il file XAML completo finale con tre pulsanti, l'accesso a sei valori condivisi:

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

Gli screenshot verificare che l'applicazione di stili coerenti e gli stili di dipendente dalla piattaforma:

[Controlli con stile ![](xaml-markup-extensions-images/sharedresources.png)](xaml-markup-extensions-images/sharedresources-large.png#lightbox)

Sebbene sia più comune definire la raccolta `Resources` nella parte superiore della pagina, tenere presente che la proprietà `Resources` è definita da `VisualElement`ed è possibile avere `Resources` raccolte in altri elementi della pagina. Ad esempio, provare ad aggiungere uno alla `StackLayout` in questo esempio:

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

Si scoprirà che il colore del testo dei pulsanti a questo punto è blu. In pratica, ogni volta che il parser XAML rileva un `StaticResource` estensione di markup, esegue la ricerca nella struttura ad albero visuale e usa il primo `ResourceDictionary` rileva che contiene tale chiave.

Uno dei tipi più comuni di oggetti archiviati nei dizionari risorse è Novell. Forms `Style`, che definisce una raccolta di impostazioni delle proprietà. Gli stili vengono descritti negli [stili](~/xamarin-forms/user-interface/styles/index.md)degli articoli.

A volte gli sviluppatori nuovi a XAML si chiedono se possono inserire un elemento visivo, ad esempio `Label` o `Button` in un `ResourceDictionary`. Sebbene sia certamente possibile, non ha senso molto altro. Lo scopo della `ResourceDictionary` consiste nel condividere gli oggetti. Un elemento visivo non può essere condivisi. La stessa istanza non può comparire due volte in una singola pagina.

## <a name="the-xstatic-markup-extension"></a>L'estensione di Markup X:Static

Nonostante le analogie dei nomi, `x:Static` e `StaticResource` sono molto diversi. `StaticResource` restituisce un oggetto da un dizionario risorse, mentre `x:Static` accede a uno dei seguenti elementi:

- un campo statico pubblico
- una proprietà statica pubblica
- un campo costante pubblico
- un membro di enumerazione.

L'estensione di markup `StaticResource` è supportata dalle implementazioni XAML che definiscono un dizionario risorse, mentre `x:Static` è una parte intrinseca di XAML, come rivela il prefisso `x`.

Di seguito sono riportati alcuni esempi che illustrano in che modo `x:Static` possibile fare riferimento in modo esplicito a campi statici e membri dell'enumerazione:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Finora, questo non è particolarmente impressionante. Tuttavia, il `x:Static` estensione di markup può anche fare riferimento a campi o proprietà statiche dal proprio codice. Ad esempio, di seguito è riportato un `AppConstants` classe che contiene alcuni campi statici che possono essere utilizzati in più pagine in un'applicazione:

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

Per fare riferimento ai campi statici di questa classe nel file XAML, è necessario trovare un modo per indicare all'interno del file XAML in cui si trova il file. A tale scopo con una dichiarazione dello spazio dei nomi XML.

È importante ricordare che i file XAML creati come parte del modello XAML di xamarin. Forms standard contengono due dichiarazioni dello spazio dei nomi XML: uno per l'accesso alle classi di xamarin. Forms e un altro per fare riferimento a tag e attributi intrinseci per XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

È necessario ulteriori dichiarazioni dello spazio dei nomi XML per accedere ad altre classi. Ogni dichiarazione dello spazio dei nomi XML aggiuntivi definisce un nuovo prefisso. Per accedere alle classi locali per l'applicazione condivisa .NET Standard libreria, ad esempio `AppConstants`, i programmatori XAML usano spesso il prefisso `local`. La dichiarazione dello spazio dei nomi deve indicare il nome dello spazio dei nomi CLR (Common Language Runtime), noto anche come nome dello spazio dei nomi .NET, C# che è il nome che viene visualizzato in una definizione di `namespace` o in una direttiva `using`:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

È anche possibile definire le dichiarazioni dello spazio dei nomi XML per gli spazi dei nomi .NET in qualsiasi assembly che fa riferimento la libreria .NET Standard. Ad esempio, di seguito è riportato un prefisso `sys` per lo spazio dei nomi .NET `System` standard, disponibile nell'assembly **netstandard** . Poiché si tratta di un altro assembly, è necessario specificare anche il nome dell'assembly, in questo caso **netstandard**:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Si noti che la parola chiave `clr-namespace` è seguita da due punti, quindi dal nome dello spazio dei nomi .NET, seguito da un punto e virgola, dalla parola chiave `assembly`, da un segno di uguale e dal nome dell'assembly.

Sì, i due punti seguono `clr-namespace` ma il segno di uguale segue `assembly`. La sintassi è stata definita in questo modo intenzionalmente: la maggior parte delle dichiarazioni dello spazio dei nomi XML fa riferimento a un URI che inizia con un nome di schema URI, ad esempio `http`, che è sempre seguito da due punti. La `clr-namespace` parte di questa stringa è progettata per simulare tale convenzione.

Entrambe le dichiarazioni dello spazio dei nomi sono incluse nell'esempio **StaticConstantsPage** . Si noti che le dimensioni del `BoxView` sono impostate su `Math.PI` e `Math.E`, ma ridimensionate in base a un fattore di 100:

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

Le dimensioni del `BoxView` risultante rispetto alla schermata sono dipendenti dalla piattaforma:

[![controlli con l'estensione di markup x:Static](xaml-markup-extensions-images/staticconstants.png)](xaml-markup-extensions-images/staticconstants-large.png#lightbox)

## <a name="other-standard-markup-extensions"></a>Altre estensioni di Markup Standard

Diverse estensioni di markup sono intrinseche di XAML e supportati in file XAML di xamarin. Forms. Alcuni di questi non vengono usati molto spesso ma sono essenziali quando sono necessarie:

- Se per impostazione predefinita una proprietà ha un valore non `null` ma si vuole impostarla su `null`, impostarla sull'estensione di markup `{x:Null}`.
- Se una proprietà è di tipo `Type`, è possibile assegnarla a un oggetto `Type` usando l'estensione di markup `{x:Type someClass}`.
- È possibile definire matrici in XAML usando l'estensione di markup `x:Array`. Questa estensione di markup dispone di un attributo obbligatorio denominato `Type` che indica il tipo degli elementi nella matrice.
- L'estensione di markup `Binding` viene illustrata nella [parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).
- L'estensione di markup `RelativeSource` viene illustrata in [associazioni relative](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="the-constraintexpression-markup-extension"></a>L'estensione di Markup ConstraintExpression

Le estensioni di markup possono disporre di proprietà, ma non vengono impostate come attributi XML. In un'estensione di markup, le impostazioni delle proprietà sono separati da virgole e virgolette non vengono visualizzati tra parentesi graffe.

Questa operazione può essere illustrata con l'estensione di markup Novell. Forms denominata `ConstraintExpression`, che viene usata con la classe `RelativeLayout`. È possibile specificare la posizione o dimensione di una visualizzazione figlio come una costante o relativo di un genitore o altra visualizzazione denominata. La sintassi della `ConstraintExpression` consente di impostare la posizione o le dimensioni di una visualizzazione utilizzando una `Factor` volte una proprietà di un'altra visualizzazione, oltre a un `Constant`. Operazioni più complesse rispetto a quello richiede codice.

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

Forse la lezione più importante da eseguire da questo esempio è riportata la sintassi dell'estensione di markup: virgolette non deve essere racchiuso tra parentesi graffe in un'estensione di markup. Quando si digita l'estensione di markup in un file XAML, è naturale a racchiuderlo tra virgolette i valori delle proprietà. Resistere alla tentazione!

Ecco il programma in esecuzione:

[![layout relativo mediante vincoli](xaml-markup-extensions-images/relativelayout.png)](xaml-markup-extensions-images/relativelayout-large.png#lightbox)

## <a name="summary"></a>Riepilogo

Le estensioni di markup XAML illustrate di seguito forniscono supporto importante per i file XAML. Tuttavia, l'estensione di markup XAML più preziosa è `Binding`, che verrà analizzata nella parte successiva della serie, [parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introduzione con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Dal data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
