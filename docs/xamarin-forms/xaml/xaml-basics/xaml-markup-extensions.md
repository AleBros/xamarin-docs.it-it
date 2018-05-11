---
title: Parte 3. Estensioni di Markup XAML
description: Le estensioni di markup XAML costituiscono una caratteristica importante in XAML che consentono di proprietà da impostare per gli oggetti o valori a cui fa riferimento indirettamente da altre origini. Le estensioni di markup XAML sono particolarmente importanti per la condivisione di oggetti e le costanti utilizzate in tutta l'applicazione di riferimento, ma è disponibile la relativa utilità maggiore nel data binding.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F4A37564-B18B-42FF-B841-9A1949895AB6
author: charlespetzold
ms.author: chape
ms.date: 3/27/2018
ms.openlocfilehash: c110223eae2bb06f64adf3e09977d97cc7b5d71b
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="part-3-xaml-markup-extensions"></a>Parte 3. Estensioni di Markup XAML

_Le estensioni di markup XAML costituiscono una caratteristica importante in XAML che consentono di proprietà da impostare per gli oggetti o valori a cui fa riferimento indirettamente da altre origini. Le estensioni di markup XAML sono particolarmente importanti per la condivisione di oggetti e le costanti utilizzate in tutta l'applicazione di riferimento, ma è disponibile la relativa utilità maggiore nel data binding._

## <a name="xaml-markup-extensions"></a>Estensioni di Markup XAML

In generale, utilizzare XAML per impostare le proprietà di un oggetto valori espliciti, ad esempio una stringa, un numero, un membro di enumerazione o una stringa che viene convertita in un valore in background.

In alcuni casi, tuttavia, proprietà invece devono fare riferimento a valori definiti altrove, o che potrebbero richiedere un'elaborazione minima dal codice in fase di esecuzione. A tale scopo, XAML *le estensioni di markup* sono disponibili.

Le estensioni di markup XAML non sono estensioni del linguaggio XML. XAML è interamente legali XML. Vengono chiamate "estensioni" perché ne viene eseguito il codice nelle classi che implementano `IMarkupExtension`. È possibile scrivere estensioni di markup personalizzata.

In molti casi, le estensioni di markup XAML sono immediatamente riconoscibile nei file XAML perché vengono visualizzati come le impostazioni degli attributi racchiusi tra parentesi graffe: {e}, ma in alcuni casi le estensioni di markup vengono visualizzati nel markup come elementi convenzionali.

## <a name="shared-resources"></a>Risorse condivise

Alcune pagine XAML contengono diverse viste con le proprietà impostate per gli stessi valori. Ad esempio, molte delle impostazioni delle proprietà per questi `Button` oggetti sono uguali:

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

Se una di queste proprietà deve essere modificato, è preferibile apportare la modifica di una sola volta, anziché i tre volte. Se si trattasse di codice, si sarebbe probabilmente essere utilizzando costanti e gli oggetti statici di sola lettura per mantenere tali valori coerenti e facile da modificare.

In XAML, una soluzione comune consiste nell'archiviare tali valori o oggetti un *dizionario risorse*. Il `VisualElement` classe definisce una proprietà denominata `Resources` di tipo `ResourceDictionary`, ovvero un dizionario con chiavi di tipo `string` e valori di tipo `object`. È possibile posizionare gli oggetti in questo dizionario e quindi farvi riferimento dal markup, tutto in XAML.

Per utilizzare un dizionario risorse in una pagina, includere una coppia di `Resources` tag di elemento di proprietà. È più pratico per inserire gli elementi nella parte superiore della pagina:

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

È inoltre necessario includere in modo esplicito `ResourceDictionary` tag:

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

Ora gli oggetti e i valori di vari tipi possono essere aggiunti al dizionario risorse. Questi tipi devono essere istanziabili. Non possono essere classi astratte, ad esempio. Questi tipi devono anche avere un costruttore pubblico senza parametri. Ogni elemento richiede una chiave del dizionario specificata con il `x:Key` attributo. Ad esempio:

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

Questi due elementi sono valori del tipo di struttura `LayoutOptions`e ognuno presenta una chiave univoca e una o due proprietà impostate. Nel codice e markup, è molto più comune per utilizzare i campi statici della `LayoutOptions`, ma è più pratico impostare le proprietà.

Ora è necessario impostare il `HorizontalOptions` e `VerticalOptions` le proprietà di questi pulsanti a queste risorse, e che viene eseguita con il `StaticResource` estensione di markup XAML:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="3"
        Rotation="-15"
        TextColor="Red"
        FontSize="24" />
```

Il `StaticResource` estensione di markup è sempre delimitato con parentesi graffe e include la chiave del dizionario.

Il nome `StaticResource` lo distingue da `DynamicResource`, che supporta inoltre xamarin. Forms. `DynamicResource` per le chiavi del dizionario associate ai valori che possono cambiare in fase di esecuzione, mentre `StaticResource` accede agli elementi dal dizionario solo una volta quando gli elementi nella pagina vengono costruiti.

Per il `BorderWidth` proprietà, è necessario archiviare un valore double nel dizionario. XAML definisce in modo appropriato i tag per i tipi di dati comuni come `x:Double` e `x:Int32`:

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

Non è necessario inserirla in tre righe. Questa voce del dizionario per l'angolo di rotazione richiede solo una riga:

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

Queste due risorse possono fare riferimento esattamente come il `LayoutOptions` valori:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="Red"
        FontSize="24" />
```

Per le risorse di tipo `Color`, è possibile utilizzare le rappresentazioni di stringa stessa utilizzata per assegnare direttamente gli attributi di questi tipi. Quando si crea la risorsa, vengono richiamati i convertitori di tipi. Di seguito è una risorsa di tipo `Color`:

```xaml
<Color x:Key="textColor">Red</Color>
```

Spesso, i programmi set una `FontSize` proprietà a un membro del `NamedSize` enumerazione, ad esempio `Large`. Il `FontSizeConverter` classe funziona in background per convertirlo in un valore di dipendente dalla piattaforma utilizzando il `Device.GetNamedSized` metodo. Tuttavia, quando si definisce una risorsa di dimensioni del carattere, rende più logico usare un valore numerico, illustrato qui come un `x:Double` tipo:

```xaml
<x:Double x:Key="fontSize">24</x:Double>
```

A questo punto tutte le proprietà ad eccezione di `Text` sono definiti dalle impostazioni di risorsa:

```xaml
<Button Text="Do this!"
        HorizontalOptions="{StaticResource horzOptions}"
        VerticalOptions="{StaticResource vertOptions}"
        BorderWidth="{StaticResource borderWidth}"
        Rotation="{StaticResource rotationAngle}"
        TextColor="{StaticResource textColor}"
        FontSize="{StaticResource fontSize}" />
```

È anche possibile usare `OnPlatform` all'interno del dizionario risorse per definire valori diversi per le piattaforme. Ecco come un `OnPlatform` oggetto può essere parte del dizionario risorse per i colori di testo diversa:

```xaml
<OnPlatform x:Key="textColor"
            x:TypeArguments="Color">
    <On Platform="iOS" Value="Red" />
    <On Platform="Android" Value="Aqua" />
    <On Platform="UWP" Value="#80FF80" />
</OnPlatform>
```

Si noti che `OnPlatform` Ottiene entrambi un `x:Key` attributo perché è un oggetto nel dizionario e un `x:TypeArguments` attributo perché è una classe generica. Il `iOS`, `Android`, e `UWP` attributi vengono convertiti in `Color` valori quando viene inizializzato l'oggetto.

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

            <x:String x:Key="fontSize">Large</x:String>
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

Le schermate verificare gli stili coerenti e gli stili dipendente dalla piattaforma:

[![](xaml-markup-extensions-images/sharedresources.png "Controlli")](xaml-markup-extensions-images/sharedresources-large.png#lightbox "concepito controlli")

Sebbene sia più comune per definire il `Resources` insieme nella parte superiore della pagina, tenere presente che il `Resources` proprietà è definita da `VisualElement`, ed è possibile disporre `Resources` raccolte su altri elementi nella pagina. Ad esempio, provare ad aggiungere uno per il `StackLayout` in questo esempio:

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

È possibile osservare che il colore del testo dei pulsanti è blu. In pratica, ogni volta che il parser XAML rileva un `StaticResource` estensione di markup, Cerca la struttura ad albero visuale e viene utilizzato il primo `ResourceDictionary` rileva contenente tale chiave.

Uno dei tipi più comuni di oggetti archiviati nei dizionari risorse è di xamarin. Forms `Style`, che definisce una raccolta di impostazioni delle proprietà. Gli stili vengono descritti nell'articolo [stili](~/xamarin-forms/user-interface/styles/index.md).

Talvolta gli sviluppatori in XAML chiedersi se possono inserire, ad esempio un elemento visivo `Label` o `Button` in un `ResourceDictionary`. Sebbene sia possibile sicuramente, può non essere più opportuno. Lo scopo del `ResourceDictionary` è la condivisione di oggetti. Un elemento visivo non può essere condivisi. La stessa istanza non può comparire due volte in una singola pagina.

## <a name="the-xstatic-markup-extension"></a>L'estensione di Markup X:Static

Nonostante le analogie dei relativi nomi, `x:Static` e `StaticResource` sono molto diversi. `StaticResource` Restituisce un oggetto da un dizionario risorse durante `x:Static` accede a una delle operazioni seguenti:

- un campo statico pubblico
- una proprietà statica pubblica
- un campo costante pubblico 
- un membro di enumerazione. 

Il `StaticResource` estensione di markup è supportato dalle implementazioni XAML che definiscono un dizionario risorse, mentre `x:Static` è una parte di intrinseca di XAML, come il `x` rivela del prefisso.

Ecco alcuni esempi che illustrano come `x:Static` può fare riferimento esplicitamente i campi statici e i membri dell'enumerazione:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="{x:Static LayoutOptions.Start}"
       HorizontalTextAlignment="{x:Static TextAlignment.Center}"
       TextColor="{x:Static Color.Aqua}" />
```

Finora, questo non è molto bene. Ma la `x:Static` estensione di markup può anche fare riferimento a proprietà o campi statici dal codice. Ad esempio, ecco un `AppConstants` classe che contiene alcuni campi statici che è possibile che si desidera utilizzare in più pagine in tutta l'applicazione:

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

Per fare riferimento i campi statici della classe nel file XAML, è necessario un modo per indicare all'interno del file XAML in cui si trova il file. A tale scopo, con una dichiarazione dello spazio dei nomi XML.

Tenere presente che i file XAML creati come parte del modello standard di xamarin. Forms XAML contengono due dichiarazioni dello spazio dei nomi XML: uno per l'accesso alle classi di xamarin. Forms e un altro per fare riferimento a tag e attributi intrinseci di XAML:

```csharp
xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
```

È necessario ulteriori dichiarazioni di spazio dei nomi XML per accedere alle altre classi. Ogni dichiarazione di spazio dei nomi XML aggiuntivo definisce un nuovo prefisso. Per accedere alle classi locale per la libreria .NET Standard di applicazione condivisa, ad esempio `AppConstants`, i programmatori XAML utilizzano spesso il prefisso `local`. La dichiarazione dello spazio dei nomi deve indicare il nome dello spazio dei nomi CLR (Common Language Runtime), noto anche come il nome di spazio dei nomi .NET, ovvero il nome visualizzato del linguaggio c# `namespace` definizione o in un `using` direttiva:

```csharp
xmlns:local="clr-namespace:XamlSamples"
```

È inoltre possibile definire le dichiarazioni dello spazio dei nomi XML per gli spazi dei nomi .NET in qualsiasi assembly cui fa riferimento la libreria .NET Standard. Ad esempio, ecco un `sys` prefisso per .NET standard `System` spazio dei nomi, ovvero nel **mscorlib** assembly, che una volta superato per "Common Runtime libreria di oggetti", ma ora significa "multilingue Standard Oggetto Runtime libreria comune." Poiché si tratta di un altro assembly, è necessario anche specificare il nome dell'assembly, in questo caso **mscorlib**:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Si noti che la parola chiave `clr-namespace` è seguita da due punti e quindi il nome dello spazio dei nomi .NET, seguito da un punto e virgola, la parola chiave `assembly`, un segno di uguale e il nome dell'assembly.

Sì, i due punti segue `clr-namespace` ma di segno di uguale seguito `assembly`. La sintassi è stata definita in questo modo deliberatamente: le dichiarazioni dello spazio dei nomi XML più fare riferimento a un URI che inizia come un nome di schema URI `http`, che è sempre seguito da due punti. Il `clr-namespace` parte di questa stringa deve simulare tale convenzione.

Entrambe queste dichiarazioni dello spazio dei nomi inclusi nel **StaticConstantsPage** esempio. Si noti che il `BoxView` quote sono impostate su `Math.PI` e `Math.E`, ma in scala di un fattore pari a 100:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
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

Le dimensioni di risultante `BoxView` rispetto allo schermo è dipendente dalla piattaforma:

 [![](xaml-markup-extensions-images/staticconstants.png "I controlli usando l'estensione di Markup X:Static")](xaml-markup-extensions-images/staticconstants-large.png#lightbox "controlli utilizzando l'estensione di Markup X:Static")

## <a name="other-standard-markup-extensions"></a>Altre estensioni di Markup Standard

Diverse estensioni di markup sono intrinseche al codice XAML e nei file XAML di xamarin. Forms è supportato. Alcune di queste non vengono utilizzate molto spesso, ma sono essenziali quando sono necessari:

-  Se una proprietà ha non `null` valore per impostazione predefinita, ma si desidera impostare `null`, impostarla sul `{x:Null}` estensione di markup.
-  Se una proprietà è di tipo `Type`, è possibile assegnarla a una `Type` utilizzando l'estensione di markup `{x:Type someClass}`.
-  È possibile definire le matrici in XAML tramite il `x:Array` estensione di markup. Questa estensione di markup è un attributo obbligatorio denominato `Type` che indica il tipo degli elementi nella matrice.
- Il `Binding` estensione di markup viene illustrata in [parte 4. Nozioni di base di associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).

## <a name="the-constraintexpression-markup-extension"></a>L'estensione di Markup ConstraintExpression

Le estensioni di markup possono disporre di proprietà, ma non vengono impostate come attributi XML. In un'estensione di markup, le impostazioni delle proprietà sono separati da virgole e virgolette non vengono visualizzati all'interno delle parentesi graffe.

Questo può essere illustrato con l'estensione di markup di xamarin. Forms denominata `ConstraintExpression`, che viene utilizzata con la `RelativeLayout` classe. È possibile specificare la posizione o dimensioni di una visualizzazione figlio come costante, o un elemento padre o altra vista denominata. La sintassi del `ConstraintExpression` consente di impostare la posizione o le dimensioni di una vista che utilizza un `Factor` volte una proprietà di un'altra visualizzazione, oltre a `Constant`. Operazioni più complesse rispetto a quello richiede il codice.

Di seguito è riportato un esempio:

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

Ad esempio la lezione più importante da prendere da questo esempio è riportata la sintassi dell'estensione di markup: virgolette non deve essere racchiuso tra parentesi graffe di un'estensione di markup. Quando si digita l'estensione di markup in un file XAML, è necessario racchiudere tra virgolette i valori delle proprietà naturale. Resistere alla tentazione!

Di seguito è riportato il programma in esecuzione:

[![](xaml-markup-extensions-images/relativelayout.png "Layout relativo utilizzando vincoli")](xaml-markup-extensions-images/relativelayout-large.png#lightbox "Layout relativo utilizzo di vincoli")

## <a name="summary"></a>Riepilogo

Le estensioni di markup XAML illustrate di seguito forniscono supporto importante per i file XAML. Ma l'estensione di markup XAML estremamente utile è probabilmente `Binding`, illustrata nella sezione successiva di questa serie, [parte 4. Nozioni di base di associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md).



## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da un'associazione dati a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
