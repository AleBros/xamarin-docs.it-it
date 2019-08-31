---
title: Novell. Forms ToolbarItem
description: La classe ToolbarItem è un tipo speciale di pulsante usato nella barra di spostamento di un'applicazione.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: b361bc988b66d38fc33ceb866a2c5ec83153f8d0
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200022"
---
# <a name="xamarinforms-toolbaritem"></a>Novell. Forms ToolbarItem

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

La classe Novell. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Forms è un tipo speciale di pulsante che può essere aggiunto alla `Page` `ToolbarItems` raccolta di un oggetto. Ogni `ToolbarItem` oggetto verrà visualizzato come pulsante nella barra di spostamento dell'applicazione. Un' `ToolbarItem` istanza può avere un'icona e apparire come una voce di menu primaria o secondaria. La `ToolbarItem` classe eredita da [`MenuItem`](xref:Xamarin.Forms.MenuItem).

Gli screenshot seguenti mostrano `ToolbarItem` gli oggetti nella barra di spostamento in iOS e Android:

!["Schermata demo di ToolbarItem in Android e iOS"](toolbaritem-images/toolbaritem-device-screenshot.png "Schermata demo di ToolbarItem in Android e iOS")

La `ToolbarItem` classe definisce le proprietà seguenti:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)è un `ToolbarItemOrder` valore di enumerazione che determina se `ToolbarItem` l'istanza viene visualizzata nel menu primario o secondario.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)valore che determina l'ordine di visualizzazione degli elementi nella `ToolbarItems` raccolta di `Page` un oggetto. `integer`

La `ToolbarItem` classe eredita le seguenti proprietà utilizzate in genere `MenuItem` dalla classe:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)è un `ICommand` oggetto che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un elemento ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)è un `object` oggetto che specifica il parametro che deve essere passato `Command`a.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)valore che determina l'icona di visualizzazione in un `ToolbarItem` oggetto. `ImageSource`
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)è un `string` oggetto che determina il testo visualizzato in `ToolbarItem` un oggetto.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) in modo che `ToolbarItem` un'istanza possa essere la destinazione delle associazioni dati.

> [!NOTE]
> Un'alternativa alla creazione di una barra [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) degli strumenti dagli oggetti consiste [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) nell'impostare la proprietà associata su una classe layout contenente più visualizzazioni. Per ulteriori informazioni, vedere [visualizzazione delle visualizzazioni nella barra di spostamento](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Creare un ToolbarItem

È `ToolbarItem` possibile creare un'istanza di un oggetto in XAML. È `Text` possibile `IconImageSource` impostare le proprietà e per determinare la modalità di visualizzazione del pulsante nella barra di spostamento. Nell'esempio seguente viene illustrato come creare un'istanza `ToolbarItem` di con alcune proprietà comuni impostate:

```xaml
<ToolbarItem Text="Example Item"
             IconImageSource="example_icon.png"
             Order="Primary"
             Priority="0" />
```

Questo esempio genera un `ToolbarItem` oggetto con testo, un'icona e viene visualizzato per primo nell'area della barra di spostamento primaria. Un `ToolbarItem` oggetto può anche essere creato nel codice e aggiunto `ToolbarItems` alla raccolta:

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

Il file rappresentato da `string`, fornito `IconImageSource` come proprietà, deve esistere in ogni progetto di piattaforma.

> [!NOTE]
> Gli asset immagine vengono gestiti in modo diverso in ogni piattaforma. Un `ImageSource` può provenire da origini che includono un file locale o una risorsa incorporata, un URI o un flusso. Per ulteriori informazioni sull'impostazione della `IconImageSource` proprietà e delle immagini in Novell. Forms, vedere [Immagini in Novell. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Comportamento dei pulsanti Definisci

La `ToolbarItem` classe eredita l' `Clicked` evento dalla `MenuItem` classe. Un gestore eventi può essere associato `Clicked` all'evento per rispondere alle scelte o ai clic sulle `ToolbarItem` istanze in XAML:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

Un gestore eventi può essere allegato anche nel codice:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

Negli esempi precedenti è stato `OnItemClicked` fatto riferimento a un gestore eventi. Nel codice seguente viene illustrata un'implementazione di esempio:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`gli oggetti possono anche usare `Command` le `CommandParameter` proprietà e per rispondere all'input dell'utente senza gestori di eventi. Per ulteriori informazioni sull' `ICommand` interfaccia e sul data binding MVVM, vedere [Novell. Forms MenuItem MVVM Behavior](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="primary-and-secondary-menus"></a>Menu primari e secondari

L' `ToolbarItemOrder` enumerazione include `Default`i `Primary`valori, `Secondary` e.

Quando la `Order` proprietà è `Primary`impostata su, l' `ToolbarItem` oggetto verrà visualizzato nella barra di spostamento principale in tutte le piattaforme. `ToolbarItem`agli oggetti viene assegnata la priorità sul titolo della pagina, che verrà troncato per creare spazio per gli elementi. Gli screenshot seguenti mostrano `ToolbarItem` gli oggetti nel menu principale in iOS e Android:

!["ToolbarItem schermata del menu primario Android e iOS"](toolbaritem-images/toolbaritem-primary-menu.png "Schermata del menu primario di ToolbarItem in Android e iOS")

Quando la `Order` proprietà è impostata su `Secondary`, il comportamento varia nelle diverse piattaforme. In UWP e Android il `Secondary` menu elementi viene visualizzato come tre punti che è possibile toccare o fare clic per rivelare gli elementi in un elenco verticale. In iOS il `Secondary` menu elementi viene visualizzato sotto la barra di spostamento come un elenco orizzontale. Gli screenshot seguenti mostrano un menu secondario in iOS e Android:

!["Schermata del menu secondario di ToolbarItem Android e iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Schermata del menu secondario di ToolbarItem in Android e iOS")

> [!WARNING]
> Il `Secondary` `ToolbarItem` comportamentodelleiconeneglioggettilacuiproprietàèimpostatasuèincoerentenellediversepiattaforme.`Order` Evitare di impostare `IconImageSource` la proprietà sugli elementi visualizzati nel menu secondario.

## <a name="related-links"></a>Collegamenti correlati

* [Demo di ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Immagini in Novell. Forms](~/xamarin-forms/user-interface/images.md)
* [MenuItem Novell. Forms](~/xamarin-forms/user-interface/menuitem.md)
