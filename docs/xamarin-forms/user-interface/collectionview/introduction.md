---
title: Introduzione a Novell. Forms di CollectionView
description: CollectionView è una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati con specifiche di layout diverse.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 89afb0f2bfe93a5f78b0cd162f2a65e585b54b4b
ms.sourcegitcommit: 43423d4018cc0d4b0b8c98a4b3da0704495eb0cf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303229"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introduzione a Novell. Forms di CollectionView

![Questa API è attualmente in versione non definitiva](~/media/shared/preview.png)

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) è una vista per presentare elenchi di dati usando specifiche di layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView). Ad esempio, le schermate seguenti mostrano un `CollectionView` che usa una griglia verticale a due colonne e che consente la selezione multipla:

[![Screenshot di un layout di griglia verticale di CollectionView, nel layout della griglia verticale di iOS e Android](introduction-images/verticalgrid-multipleselection.png "CollectionView con selezione multipla")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Layout griglia verticale di CollectionView con selezione multipla")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) è disponibile in Novell. forms 4,0. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la seguente riga di codice alla classe `AppDelegate` in iOS o alla classe `MainActivity` in Android o al `App.xaml.cs` su UWP prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) è disponibile in iOS e Android, ma è [disponibile solo parzialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) nella piattaforma UWP (Universal Windows Platform).

## <a name="collectionview-and-listview-differences"></a>Differenze di CollectionView e ListView

Sebbene le API [`CollectionView`](xref:Xamarin.Forms.CollectionView) e [`ListView`](xref:Xamarin.Forms.ListView) siano simili, esistono alcune differenze rilevanti:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) dispone di un modello di layout flessibile, che consente di presentare i dati in verticale o in orizzontale, in un elenco o in una griglia.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta selezioni singole e multiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) non ha alcun concetto di celle. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilizza automaticamente la virtualizzazione fornita dai controlli nativi sottostanti.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) riduce la superficie dell'API di [`ListView`](xref:Xamarin.Forms.ListView). Molti eventi e proprietà di [`ListView`](xref:Xamarin.Forms.ListView) non sono presenti nel `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) non include separatori predefiniti.

## <a name="move-from-listview-to-collectionview"></a>Passa da ListView a CollectionView

le implementazioni [`ListView`](xref:Xamarin.Forms.ListView) nelle implementazioni di Novell. Forms esistenti possono essere migrate alle implementazioni di [`CollectionView`](xref:Xamarin.Forms.CollectionView) con l'aiuto della tabella seguente:

| Concetto | API ListView | CollectionView |
|---|---|---|
| Data | `ItemsSource` | Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene popolato con i dati impostando la relativa proprietà `ItemsSource`. Per altre informazioni, vedere [popolare un oggetto CollectionView con i dati](populate-data.md#populate-a-collectionview-with-data). |
| Aspetto elemento | `ItemTemplate` | L'aspetto di ogni elemento in un [`CollectionView`](xref:Xamarin.Forms.CollectionView) può essere definito impostando la proprietà `ItemTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Per altre informazioni, vedere [definire l'aspetto dell'elemento](populate-data.md#define-item-appearance). |
| Celle | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) non ha alcun concetto di celle. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco. |
| Separatori di riga | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) non include separatori predefiniti. Se necessario, è possibile fornire tali elementi nel modello di elemento. |
| Selection | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta selezioni singole e multiple. Per ulteriori informazioni, vedere [Novell. Forms CollectionView Selection](selection.md). |
| Altezza riga | `HasUnevenRows`, `RowHeight` | In un `CollectionView`, l'altezza della riga di ogni elemento è determinata dalla proprietà `ItemSizingStrategy`. Per ulteriori informazioni, vedere [ridimensionamento di elementi](layout.md#item-sizing).|
| Memorizzazione nella cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilizza automaticamente la virtualizzazione fornita dai controlli nativi sottostanti. |
| Intestazioni e piè di pagina | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) può presentare un'intestazione e un piè di pagina che scorrono con gli elementi dell'elenco, tramite le proprietà `Header`, `Footer`, `HeaderTemplate` e `FooterTemplate`. Per ulteriori informazioni, vedere [intestazioni e piè](layout.md#headers-and-footers)di pagina. |
| Raggruppamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) Visualizza i dati raggruppati correttamente impostando la relativa proprietà `IsGrouped` su `true`. Le intestazioni di gruppo e i piè di pagina di gruppo possono essere personalizzati impostando le proprietà `GroupHeaderTemplate` e `GroupFooterTemplate` su oggetti [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Per ulteriori informazioni, vedere [raggruppamento di Novell. Forms CollectionView](grouping.md). |
| Pull per aggiornare | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Il pull per l'aggiornamento non è attualmente supportato in `CollectionView`, ma verrà aggiunto in una versione futura. |
| Azioni di contesto | `ContextActions` | Le azioni di contesto non sono attualmente supportate in `CollectionView`, ma verranno aggiunte in una versione futura. |
| Scorrimento | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce i metodi `ScrollTo`, che scorrono gli elementi nella visualizzazione. Per ulteriori informazioni, vedere [scorrimento](scrolling.md). |

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
