---
title: Novell. Forms ToolbarItem
description: La classe ToolbarItem è un tipo speciale di pulsante usato nella barra di spostamento di un'applicazione.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: 0812347e85b0ccb6aa0bbb16649a89bb4d961c9b
ms.sourcegitcommit: a14edebf00f3e0f8944e59042ca7aa5c42173e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72780346"
---
# <a name="xamarinforms-toolbaritem"></a>Novell. Forms ToolbarItem

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

La classe [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Novell. Forms è un tipo speciale di pulsante che può essere aggiunto alla raccolta di `ToolbarItems` di un oggetto `Page`. Ogni oggetto `ToolbarItem` verrà visualizzato come pulsante nella barra di spostamento dell'applicazione. Un'istanza di `ToolbarItem` può avere un'icona e apparire come una voce di menu primaria o secondaria. La classe `ToolbarItem` eredita da [`MenuItem`](xref:Xamarin.Forms.MenuItem).

Gli screenshot seguenti mostrano `ToolbarItem` oggetti nella barra di spostamento in iOS e Android:

!["Schermata demo di ToolbarItem in Android e iOS"](toolbaritem-images/toolbaritem-device-screenshot.png "Schermata demo di ToolbarItem in Android e iOS")

La classe `ToolbarItem` definisce le proprietà seguenti:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) è un valore `ToolbarItemOrder` enum che determina se l'istanza di `ToolbarItem` viene visualizzata nel menu primario o secondario.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority) è un valore `integer` che determina l'ordine di visualizzazione degli elementi nella raccolta `ToolbarItems` di un oggetto `Page`.

La classe `ToolbarItem` eredita le seguenti proprietà utilizzate in genere dalla classe `MenuItem`:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) è un `ICommand` che consente di associare le azioni dell'utente, ad esempio le manopole o i clic, ai comandi definiti in un ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) è un `object` che specifica il parametro che deve essere passato al `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) è un valore `ImageSource` che determina l'icona di visualizzazione in un oggetto `ToolbarItem`.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) è un `string` che determina il testo visualizzato in un oggetto `ToolbarItem`.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) in modo che un'istanza di `ToolbarItem` possa essere la destinazione delle associazioni dati.

> [!NOTE]
> Un'alternativa alla creazione di una barra degli strumenti da [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) oggetti consiste nell'impostare la proprietà [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) associata a una classe layout contenente più visualizzazioni. Per ulteriori informazioni, vedere [visualizzazione delle visualizzazioni nella barra di spostamento](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Creare un ToolbarItem

È possibile creare un'istanza di un oggetto `ToolbarItem` in XAML. È possibile impostare le proprietà `Text` e `IconImageSource` per determinare la modalità di visualizzazione del pulsante nella barra di spostamento. Nell'esempio seguente viene illustrato come creare un'istanza di un `ToolbarItem` con alcune proprietà comuni impostate e come aggiungerlo alla raccolta di `ToolbarItems` di un `ContentPage`:

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

Questo esempio genera un oggetto `ToolbarItem` con testo, un'icona e viene visualizzato per primo nell'area della barra di spostamento primaria. È anche possibile creare un `ToolbarItem` nel codice e aggiungerlo alla raccolta `ToolbarItems`:

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

Il file rappresentato dal `string`, fornito come proprietà `IconImageSource`, deve esistere in ogni progetto di piattaforma.

> [!NOTE]
> Gli asset immagine vengono gestiti in modo diverso in ogni piattaforma. Un `ImageSource` può provenire da origini che includono un file locale o una risorsa incorporata, un URI o un flusso. Per ulteriori informazioni sull'impostazione della proprietà `IconImageSource` e delle immagini in Novell. Forms, vedere [Immagini in Novell. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Comportamento dei pulsanti Definisci

La classe `ToolbarItem` eredita l'evento `Clicked` dalla classe `MenuItem`. Un gestore eventi può essere associato all'evento `Clicked` per rispondere alle operazioni di tocco o clic sulle istanze di `ToolbarItem` in XAML:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

Un gestore eventi può essere allegato anche nel codice:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

Negli esempi precedenti è stato fatto riferimento a un gestore eventi `OnItemClicked`. Nel codice seguente viene illustrata un'implementazione di esempio:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem` oggetti possono inoltre utilizzare le proprietà `Command` e `CommandParameter` per rispondere all'input dell'utente senza gestori eventi. Per ulteriori informazioni sull'interfaccia `ICommand` e sul data binding MVVM, vedere [Novell. Forms MenuItem MVVM Behavior](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="primary-and-secondary-menus"></a>Menu primari e secondari

Il `ToolbarItemOrder` enum dispone di valori `Default`, `Primary` e `Secondary`.

Quando la proprietà `Order` è impostata su `Primary`, l'oggetto `ToolbarItem` verrà visualizzato nella barra di spostamento principale in tutte le piattaforme. per gli oggetti `ToolbarItem` viene assegnata la priorità al titolo della pagina, che verrà troncato per creare spazio per gli elementi. Gli screenshot seguenti mostrano `ToolbarItem` oggetti nel menu principale in iOS e Android:

!["ToolbarItem schermata del menu primario Android e iOS"](toolbaritem-images/toolbaritem-primary-menu.png "Schermata del menu primario di ToolbarItem in Android e iOS")

Quando la proprietà `Order` è impostata su `Secondary`, il comportamento varia a seconda delle diverse piattaforme. In UWP e Android il menu elementi `Secondary` viene visualizzato come tre punti che è possibile toccare o fare clic per rivelare gli elementi in un elenco verticale. In iOS il menu elementi `Secondary` viene visualizzato sotto la barra di spostamento come elenco orizzontale. Gli screenshot seguenti mostrano un menu secondario in iOS e Android:

!["Schermata del menu secondario di ToolbarItem Android e iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Schermata del menu secondario di ToolbarItem in Android e iOS")

> [!WARNING]
> Il comportamento delle icone negli oggetti `ToolbarItem` la cui proprietà `Order` è impostata su `Secondary` non è coerente nelle diverse piattaforme. Evitare di impostare la proprietà `IconImageSource` per gli elementi visualizzati nel menu secondario.

## <a name="related-links"></a>Collegamenti correlati

* [Demo di ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Immagini in Novell. Forms](~/xamarin-forms/user-interface/images.md)
* [MenuItem Novell. Forms](~/xamarin-forms/user-interface/menuitem.md)
