---
title: Xamarin.FormsToolbarItem
description: La classe ToolbarItem è un tipo speciale di pulsante usato nella barra di spostamento di un'applicazione.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46aba32ebbae1646b9af00877bba530b619210cd
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138216"
---
# <a name="xamarinforms-toolbaritem"></a>Xamarin.FormsToolbarItem

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

La Xamarin.Forms [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) classe è un tipo speciale di pulsante che può essere aggiunto alla raccolta di un `Page` oggetto `ToolbarItems` . Ogni `ToolbarItem` oggetto verrà visualizzato come pulsante nella barra di spostamento dell'applicazione. Un' `ToolbarItem` istanza può avere un'icona e apparire come una voce di menu primaria o secondaria. La `ToolbarItem` classe eredita da [`MenuItem`](xref:Xamarin.Forms.MenuItem) .

Gli screenshot seguenti mostrano `ToolbarItem` gli oggetti nella barra di spostamento in iOS e Android:

!["Schermata demo di ToolbarItem in Android e iOS"](toolbaritem-images/toolbaritem-device-screenshot.png "Schermata demo di ToolbarItem in Android e iOS")

La `ToolbarItem` classe definisce le proprietà seguenti:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)è un `ToolbarItemOrder` valore di enumerazione che determina se l' `ToolbarItem` istanza viene visualizzata nel menu primario o secondario.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)`integer`valore che determina l'ordine di visualizzazione degli elementi nella raccolta di un `Page` oggetto `ToolbarItems` .

La `ToolbarItem` classe eredita le seguenti proprietà utilizzate in genere dalla `MenuItem` classe:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)è un oggetto `ICommand` che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un elemento ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)è un oggetto `object` che specifica il parametro che deve essere passato a `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)`ImageSource`valore che determina l'icona di visualizzazione in un `ToolbarItem` oggetto.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)è un `string` oggetto che determina il testo visualizzato in un `ToolbarItem` oggetto.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti in modo che un' `ToolbarItem` istanza possa essere la destinazione delle associazioni dati.

> [!NOTE]
> Un'alternativa alla creazione di una barra degli strumenti dagli [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) oggetti consiste nell'impostare la [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) proprietà associata su una classe layout contenente più visualizzazioni. Per ulteriori informazioni, vedere [visualizzazione delle visualizzazioni nella barra di spostamento](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Creare un ToolbarItem

`ToolbarItem`È possibile creare un'istanza di un oggetto in XAML. `Text` `IconImageSource` È possibile impostare le proprietà e per determinare la modalità di visualizzazione del pulsante nella barra di spostamento. Nell'esempio seguente viene illustrato come creare un'istanza di un oggetto `ToolbarItem` con alcune proprietà comuni impostate e come aggiungerlo alla `ContentPage` `ToolbarItems` raccolta:

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

Questo esempio genera un oggetto con `ToolbarItem` testo, un'icona e viene visualizzato per primo nell'area della barra di spostamento primaria. Un oggetto `ToolbarItem` può anche essere creato nel codice e aggiunto alla `ToolbarItems` raccolta:

```csharp
ToolbarItem item = new ToolbarItem
{
    Text = "Example Item",
    IconImageSource = ImageSource.FromFile("example_icon.png"),
    Order = ToolbarItemOrder.Primary,
    Priority = 0
};

// "this" refers to a Page object
this.ToolbarItems.Add(item);
```

Il file rappresentato da `string` , fornito come `IconImageSource` proprietà, deve esistere in ogni progetto di piattaforma.

> [!NOTE]
> Gli asset immagine vengono gestiti in modo diverso in ogni piattaforma. Un `ImageSource` può provenire da origini che includono un file locale o una risorsa incorporata, un URI o un flusso. Per ulteriori informazioni sull'impostazione della `IconImageSource` proprietà e delle immagini in Xamarin.Forms , vedere [images in Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Comportamento dei pulsanti Definisci

La `ToolbarItem` classe eredita l' `Clicked` evento dalla `MenuItem` classe. Un gestore eventi può essere associato all' `Clicked` evento per rispondere alle scelte o ai clic sulle `ToolbarItem` istanze in XAML:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

Un gestore eventi può essere allegato anche nel codice:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

Negli esempi precedenti è stato fatto riferimento a un `OnItemClicked` gestore eventi. Nel codice seguente viene illustrata un'implementazione di esempio:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`gli oggetti possono anche usare `Command` le `CommandParameter` proprietà e per rispondere all'input dell'utente senza gestori di eventi. Per ulteriori informazioni sull' `ICommand` interfaccia e sul data binding MVVM, vedere [ Xamarin.Forms MenuItem MVVM Behavior](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>Abilitare o disabilitare un ToolbarItem in fase di esecuzione

Per abilitare la disabilitazione di un oggetto in fase di `ToolbarItem` esecuzione, associare la relativa `Command` proprietà a un' `ICommand` implementazione di e assicurarsi che un `canExecute` delegato abiliti e disattivi l'oggetto in `ICommand` base alle esigenze.

Per altre informazioni, vedere [abilitare o disabilitare un MenuItem in fase di esecuzione](menuitem.md#enable-or-disable-a-menuitem-at-runtime).

## <a name="primary-and-secondary-menus"></a>Menu primari e secondari

L' `ToolbarItemOrder` enumerazione include `Default` `Primary` `Secondary` i valori, e.

Quando la `Order` proprietà è impostata su `Primary` , l' `ToolbarItem` oggetto verrà visualizzato nella barra di spostamento principale in tutte le piattaforme. `ToolbarItem`agli oggetti viene assegnata la priorità sul titolo della pagina, che verrà troncato per creare spazio per gli elementi. Gli screenshot seguenti mostrano `ToolbarItem` gli oggetti nel menu principale in iOS e Android:

!["ToolbarItem schermata del menu primario Android e iOS"](toolbaritem-images/toolbaritem-primary-menu.png "Schermata del menu primario di ToolbarItem in Android e iOS")

Quando la `Order` proprietà è impostata su `Secondary` , il comportamento varia nelle diverse piattaforme. In UWP e Android il `Secondary` menu elementi viene visualizzato come tre punti che è possibile toccare o fare clic per rivelare gli elementi in un elenco verticale. In iOS il `Secondary` menu elementi viene visualizzato sotto la barra di spostamento come un elenco orizzontale. Gli screenshot seguenti mostrano un menu secondario in iOS e Android:

!["Schermata del menu secondario di ToolbarItem Android e iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Schermata del menu secondario di ToolbarItem in Android e iOS")

> [!WARNING]
> Il comportamento delle icone negli `ToolbarItem` oggetti la cui `Order` proprietà è impostata su `Secondary` è incoerente nelle diverse piattaforme. Evitare di impostare la `IconImageSource` proprietà sugli elementi visualizzati nel menu secondario.

## <a name="related-links"></a>Collegamenti correlati

* [Demo di ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Immagini inXamarin.Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin.FormsMenuItem](~/xamarin-forms/user-interface/menuitem.md)
