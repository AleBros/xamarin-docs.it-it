---
title: Introduzione a Xamarin.Forms di CollectionView
description: CollectionView è una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati con specifiche di layout diverse.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: c04b5250bcdc575adc5aaff73901347e1e476b07
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489336"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introduzione a Xamarin.Forms di CollectionView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) è una vista per presentare elenchi di dati usando specifiche di layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente ai [`ListView`](xref:Xamarin.Forms.ListView). Ad esempio, le schermate seguenti mostrano un `CollectionView` che usa una griglia verticale a due colonne e che consente la selezione multipla:

[![Screenshot di un layout di griglia verticale di CollectionView, in iOS e Android](introduction-images/verticalgrid-multipleselection.png "Layout griglia verticale di CollectionView con selezione multipla")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Layout griglia verticale di CollectionView con selezione multipla")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) è disponibile in Xamarin.forms 4,3.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) è disponibile in iOS e Android, ma è [disponibile solo parzialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) nella piattaforma UWP (Universal Windows Platform).

## <a name="collectionview-and-listview-differences"></a>Differenze di CollectionView e ListView

Sebbene le API [`CollectionView`](xref:Xamarin.Forms.CollectionView) e [`ListView`](xref:Xamarin.Forms.ListView) siano simili, esistono alcune differenze importanti:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) dispone di un modello di layout flessibile, che consente di presentare i dati verticalmente o orizzontalmente, in un elenco o in una griglia.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta selezioni singole e multiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) non ha alcun concetto di celle. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilizza automaticamente la virtualizzazione fornita dai controlli nativi sottostanti.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) riduce la superficie dell'API del [`ListView`](xref:Xamarin.Forms.ListView). Molti eventi e proprietà di [`ListView`](xref:Xamarin.Forms.ListView) non sono presenti nel `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) non include separatori predefiniti.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) genererà un'eccezione se il relativo [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) viene aggiornato dal thread dell'interfaccia utente.

## <a name="move-from-listview-to-collectionview"></a>Passa da ListView a CollectionView

è possibile eseguire la migrazione delle implementazioni [`ListView`](xref:Xamarin.Forms.ListView) nelle implementazioni di Xamarin.Forms esistenti in [`CollectionView`](xref:Xamarin.Forms.CollectionView) implementazioni con la seguente tabella:

| Concetto | API ListView | CollectionView |
|---|---|---|
| Data | `ItemsSource` | Una [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene popolata con i dati impostando la relativa proprietà `ItemsSource`. Per altre informazioni, vedere [popolare un oggetto CollectionView con i dati](populate-data.md#populate-a-collectionview-with-data). |
| Aspetto elemento | `ItemTemplate` | L'aspetto di ogni elemento in un [`CollectionView`](xref:Xamarin.Forms.CollectionView) può essere definito impostando la proprietà `ItemTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Per altre informazioni, vedere [definire l'aspetto dell'elemento](populate-data.md#define-item-appearance). |
| Celle | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) non ha un concetto di celle e pertanto nessun concetto di indicatori di divulgazione. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco. |
| Separatori di riga | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) non include separatori predefiniti. Se necessario, è possibile fornire tali elementi nel modello di elemento. |
| Selection | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) supporta selezioni singole e multiple. Per ulteriori informazioni, vedere [Xamarin.Forms CollectionView Selection](selection.md). |
| Altezza riga | `HasUnevenRows`, `RowHeight` | In una `CollectionView`, l'altezza della riga di ogni elemento è determinata dalla proprietà `ItemSizingStrategy`. Per ulteriori informazioni, vedere [ridimensionamento di elementi](layout.md#item-sizing).|
| Memorizzazione nella cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa automaticamente la virtualizzazione fornita dai controlli nativi sottostanti. |
| Intestazioni e piè di pagina | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) possibile presentare un'intestazione e un piè di pagina che scorrono con gli elementi dell'elenco, tramite le proprietà `Header`, `Footer`, `HeaderTemplate`e `FooterTemplate`. Per ulteriori informazioni, vedere [intestazioni e piè](layout.md#headers-and-footers)di pagina. |
| Raggruppamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) Visualizza i dati raggruppati correttamente impostando la relativa proprietà `IsGrouped` su `true`. Le intestazioni di gruppo e i piè di pagina di gruppo possono essere personalizzati impostando le proprietà `GroupHeaderTemplate` e `GroupFooterTemplate` su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetti. Per ulteriori informazioni, vedere [raggruppamento di Xamarin.Forms CollectionView](grouping.md). |
| Aggiornamento tramite trascinamento verso il basso | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Il pull per aggiornare la funzionalità è supportato impostando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) come figlio di un `RefreshView`. Per altre informazioni, vedere [pull to refresh](populate-data.md#pull-to-refresh). |
| Voci del menu di scelta rapida | `ContextActions` | Le voci del menu di scelta rapida sono supportate impostando un `SwipeView` come visualizzazione radice nella [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto di ogni elemento di dati nella [`CollectionView`](xref:Xamarin.Forms.CollectionView). Per ulteriori informazioni, vedere [menu di scelta rapida](populate-data.md#context-menus). |
| Scorrimento | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) definisce `ScrollTo` metodi che scorrono gli elementi nella visualizzazione. Per ulteriori informazioni, vedere [scorrimento](scrolling.md). |

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
