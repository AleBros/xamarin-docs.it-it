---
title: Xamarin.FormsIntroduzione a CollectionView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d6a09ead9c3def2f58ad2755de4574f6d6e331e8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136435"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin.FormsIntroduzione a CollectionView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)è una vista per la presentazione di elenchi di dati con specifiche di layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView) . Ad esempio, le schermate seguenti mostrano un `CollectionView` che usa una griglia verticale a due colonne e che consente la selezione multipla:

[![Screenshot di un layout di griglia verticale di CollectionView, in iOS e Android](introduction-images/verticalgrid-multipleselection.png "Layout griglia verticale di CollectionView con selezione multipla")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Layout griglia verticale di CollectionView con selezione multipla")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)è disponibile da Xamarin.Forms 4,3.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)è disponibile in iOS e Android, ma è [disponibile solo parzialmente](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) nella piattaforma UWP (Universal Windows Platform).

## <a name="collectionview-and-listview-differences"></a>Differenze di CollectionView e ListView

Sebbene le [`CollectionView`](xref:Xamarin.Forms.CollectionView) [`ListView`](xref:Xamarin.Forms.ListView) API e siano simili, esistono alcune differenze rilevanti:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)dispone di un modello di layout flessibile che consente di presentare i dati verticalmente o orizzontalmente in un elenco o in una griglia.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta selezioni singole e multiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)non ha un concetto di celle. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)utilizza automaticamente la virtualizzazione fornita dai controlli nativi sottostanti.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)riduce la superficie dell'API di [`ListView`](xref:Xamarin.Forms.ListView) . Molte proprietà ed eventi di [`ListView`](xref:Xamarin.Forms.ListView) non sono presenti in `CollectionView` .
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)non include separatori predefiniti.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)genererà un'eccezione se [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) viene aggiornata dal thread dell'interfaccia utente.

## <a name="move-from-listview-to-collectionview"></a>Passa da ListView a CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)Xamarin.Formsè possibile eseguire la migrazione delle implementazioni nelle implementazioni esistenti alle [`CollectionView`](xref:Xamarin.Forms.CollectionView) implementazioni con l'aiuto della tabella seguente:

| Concetto | API ListView | CollectionView |
|---|---|---|
| Data | `ItemsSource` | Un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) viene popolato con i dati impostando la `ItemsSource` Proprietà. Per altre informazioni, vedere [popolare un oggetto CollectionView con i dati](populate-data.md#populate-a-collectionview-with-data). |
| Aspetto elemento | `ItemTemplate` | L'aspetto di ogni elemento in un oggetto [`CollectionView`](xref:Xamarin.Forms.CollectionView) può essere definito impostando la `ItemTemplate` proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Per altre informazioni, vedere [definire l'aspetto dell'elemento](populate-data.md#define-item-appearance). |
| Celle | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)non ha alcun concetto di celle e pertanto non prevede alcun concetto di indicatori di divulgazione. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco. |
| Separatori di riga | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)non include separatori predefiniti. Se necessario, è possibile fornire tali elementi nel modello di elemento. |
| Selezione | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta selezioni singole e multiple. Per altre informazioni, vedere [ Xamarin.Forms selezione di CollectionView](selection.md). |
| Altezza riga | `HasUnevenRows`, `RowHeight` | In `CollectionView` , l'altezza della riga di ogni elemento è determinata dalla `ItemSizingStrategy` Proprietà. Per ulteriori informazioni, vedere [ridimensionamento di elementi](layout.md#item-sizing).|
| Memorizzazione nella cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)Usa automaticamente la virtualizzazione fornita dai controlli nativi sottostanti. |
| Intestazioni e piè di pagina | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)può presentare un'intestazione e un piè di pagina che scorrono con gli elementi dell'elenco, tramite `Header` le `Footer` proprietà,, `HeaderTemplate` e `FooterTemplate` . Per ulteriori informazioni, vedere [intestazioni e piè](layout.md#headers-and-footers)di pagina. |
| Raggruppamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)Visualizza i dati raggruppati correttamente impostando la relativa `IsGrouped` proprietà su `true` . Le intestazioni di gruppo e i piè di pagina di gruppo possono essere personalizzati impostando le `GroupHeaderTemplate` `GroupFooterTemplate` proprietà e su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetti. Per ulteriori informazioni, vedere [ Xamarin.Forms raggruppamento di CollectionView](grouping.md). |
| Aggiornamento tramite trascinamento verso il basso | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Il pull per aggiornare la funzionalità è supportato impostando [`CollectionView`](xref:Xamarin.Forms.CollectionView) come figlio di un oggetto `RefreshView` . Per altre informazioni, vedere [pull to refresh](populate-data.md#pull-to-refresh). |
| Voci del menu di scelta rapida | `ContextActions` | Le voci del menu di scelta rapida sono supportate impostando `SwipeView` come visualizzazione radice nell'oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che definisce l'aspetto di ogni elemento di dati in [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Per ulteriori informazioni, vedere [menu di scelta rapida](populate-data.md#context-menus). |
| Scorrimento | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce i `ScrollTo` metodi che scorrono gli elementi nella visualizzazione. Per ulteriori informazioni, vedere [scorrimento](scrolling.md). |

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
