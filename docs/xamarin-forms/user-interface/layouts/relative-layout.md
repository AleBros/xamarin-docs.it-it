---
title: RelativeLayout
description: Utilizzare RelativeLayout per creare interfacce utente che si adattano per adattare le dimensioni dello schermo.
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: f23c00fb40ba661cbc1633b61a898a9437fdee98
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847020"
---
# <a name="relativelayout"></a>RelativeLayout

`RelativeLayout` viene utilizzato per posizione e le viste di dimensioni rispetto alla proprietà delle viste layout o di pari livello. A differenza di `AbsoluteLayout`, `RelativeLayout` non hanno il concetto di ancoraggio mobile e non dispone di funzionalità per il posizionamento di elementi rispetto alla parte inferiore o bordi a destra del layout. `RelativeLayout` supporta il posizionamento di elementi di fuori di un proprio limiti.

[![](relative-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](relative-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Scopo

`RelativeLayout` può essere utilizzato per posizionare le viste sullo schermo rispetto al layout complessivo o ad altre viste.

![](relative-layout-images/flag.png "Esplorazione RelativeLayout")

## <a name="usage"></a>Utilizzo

### <a name="understanding-constraints"></a>Informazioni sui vincoli

Posizionamento e il ridimensionamento di una visualizzazione all'interno di un `RelativeLayout` viene eseguita con vincoli. Un'espressione di vincolo può includere le informazioni seguenti:

- **Tipo** &ndash; indica se il vincolo è relativo all'oggetto padre o a un'altra visualizzazione.
- **Proprietà** &ndash; la proprietà da utilizzare come base per il vincolo.
- **Fattore di** &ndash; fattore da applicare al valore della proprietà.
- **Costante** &ndash; il valore da utilizzare come offset del valore.
- **ElementName** &ndash; il nome della vista che il vincolo è relativo a.

In XAML, i vincoli vengono espressi come `ConstraintExpression`s. Si consideri l'esempio seguente:

```xaml
<BoxView Color="Green" WidthRequest="50" HeightRequest="50"
    RelativeLayout.XConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Width,
                             Factor=0.5,
                             Constant=-100}"
    RelativeLayout.YConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Height,
                             Factor=0.5,
                             Constant=-100}" />
```

In c#, i vincoli vengono espressi in modo leggermente diverso, utilizzando funzioni anziché le espressioni nella vista. I vincoli sono specificati come argomenti per il layout `Add` metodo:

```csharp
layout.Children.Add(box, Constraint.RelativeToParent((parent) =>
    {
      return (.5 * parent.Width) - 100;
    }),
    Constraint.RelativeToParent((parent) =>
    {
        return (.5 * parent.Height) - 100;
    }),
    Constraint.Constant(50), Constraint.Constant(50));
```

Tenere presente i seguenti aspetti di layout precedente:

- Il `x` e `y` i vincoli sono specificati con le proprie vincoli.
- In c#, i relativi vincoli vengono definiti come funzioni. Concetti come `Factor` non sono presenti, ma può essere implementata manualmente.
- La casella `x` coordinata è definita come metà della larghezza dell'elemento padre, -100.
- La casella `y` coordinata è definita come metà dell'altezza dell'elemento padre, -100.

> [!NOTE]
> A causa della modalità sono definiti vincoli, è possibile creare layout più complessi in c# che può essere specificato con XAML.

Entrambi gli esempi precedenti definiscono i vincoli come `RelativeToParent` &ndash; , ovvero i relativi valori sono relativa all'elemento padre. È inoltre possibile definire vincoli rispetto alla visualizzazione di un altro. In questo modo per layout più intuitivo (developer) e può rendere più immediatamente evidente lo scopo del codice di layout.

Prendere in considerazione un layout in cui un elemento deve essere inferiore a un altro 20 pixel. Se entrambi gli elementi sono definiti con i valori costanti, il più basso è possibile che il relativo `Y` vincolo definito come una costante che è maggiore di 20 pixel di `Y` vincolo dell'elemento superiore. Tale approccio fallisce se superiore viene posizionato l'elemento con una proporzione, in modo che non si conosce la dimensione in pixel. In tal caso, è più solido vincolare l'elemento in base alla posizione dell'elemento:

```xaml
<RelativeLayout>
    <BoxView Color="Red" x:Name="redBox"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent,
            Property=Height,Factor=.15,Constant=0}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=1,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.8,Constant=0}" />
    <BoxView Color="Blue"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=Y,Factor=1,Constant=20}"
        RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=X,Factor=1,Constant=20}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=.5,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.5,Constant=0}" />
</RelativeLayout>
```

Per eseguire lo stesso layout in c#:

```csharp
layout.Children.Add (redBox, Constraint.RelativeToParent ((parent) => {
        return parent.X;
    }), Constraint.RelativeToParent ((parent) => {
        return parent.Y * .15;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .8;
    }));
layout.Children.Add (blueBox, Constraint.RelativeToView (redBox, (Parent, sibling) => {
        return sibling.X + 20;
    }), Constraint.RelativeToView (blueBox, (parent, sibling) => {
        return sibling.Y + 20;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width * .5;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .5;
    }));
```

Ciò produce il seguente file di output, con posizione della casella blu determinata _relativo_ alla posizione della casella di colore rossa:

![](relative-layout-images/red-blue-box.png "RelativeLayout con BoxViews rosso e blu")

### <a name="sizing"></a>Ridimensionamento

Viste disposti dalla `RelativeLayout` sono disponibili due opzioni per specificare le dimensioni:

- `HeightRequest & WidthRequest`
- `RelativeLayout.WidthConstraint` & `RelativeLayout.HeightConstraint`

`HeightRequest` e `WidthRequest` specificare l'altezza desiderata e la larghezza della visualizzazione, ma può essere sostituito dal layout, in base alle esigenze. `WidthConstraint` e `HeightConstraint` supporta l'impostazione di altezza e larghezza come un valore rispetto alla proprietà del layout o un'altra visualizzazione o come un valore costante.

## <a name="exploring-a-complex-layout"></a>Esplorazione di un Layout complesso
Ogni layout di disporre di vantaggi e svantaggi per la creazione di un layout specifico. Questa serie di articoli di layout, un'app di esempio è stata creata con lo stesso layout di pagina implementato utilizzando tre layout diversi.

Si consideri il codice XAML seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.RelativeLayoutPage"
BackgroundColor="Maroon"
Title="RelativeLayout">
    <ContentPage.Content>
    <ScrollView>
      <RelativeLayout>
        <BoxView Color="Gray" HeightRequest="100"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Button BorderRadius="35" x:Name="imageCircleBack"
            BackgroundColor="Maroon" HeightRequest="70" WidthRequest="70" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.5, Constant = -35}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=70}" />
        <Button BorderRadius="30" BackgroundColor="Red" HeightRequest="60"
            WidthRequest="60" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=imageCircleBack, Property=X, Factor=1,Constant=5}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=75}" />
        <Label Text="User Name" FontAttributes="Bold" FontSize="26"
            HorizontalTextAlignment="Center" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=140}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Entry Text="Bio + Hashtags" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=180}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <RelativeLayout BackgroundColor="White" RelativeLayout.YConstraint="
            {ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=220}" HeightRequest="60" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" >
            <BoxView BackgroundColor="Black" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=5}" />
            <BoxView BackgroundColor="Maroon" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=}" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=60}" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=55}" />
        </RelativeLayout>
        <RelativeLayout Padding="5,0,0,0">
          <Label FontSize="14" Text="Age:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=305}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=280}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Interests:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=345}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=320}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
            LineBreakMode="WordWrap"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=395}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
            BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=370}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
      </RelativeLayout>
    </ScrollView>
  </ContentPage.Content>
</ContentPage>
```

Il codice precedente produce lo schema seguente:

![](relative-layout-images/relative.png "RelativeLayout complessi")

Si noti che `RelativeLayouts`s sono annidate, perché in alcuni casi la nidificazione di layout può essere più semplice rispetto a tutti gli elementi all'interno del layout stesso presentazione. Si noti anche che alcuni elementi sono `RelativeToView`, perché in questo modo per layout semplici e intuitive quando le relazioni tra le visualizzazioni di posizionamento della Guida.


## <a name="related-links"></a>Collegamenti correlati

- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
