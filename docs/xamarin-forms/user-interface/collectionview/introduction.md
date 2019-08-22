---
title: Introduzione a Novell. Forms di CollectionView
description: CollectionView è una visualizzazione flessibile ed efficiente per la presentazione di elenchi di dati con specifiche di layout diverse.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 5832cb28162602a41d31026bc4a0ed54ac6bfb34
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888604"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introduzione a Novell. Forms di CollectionView

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) è una vista per presentare elenchi di dati usando specifiche di layout diverse. Mira a offrire un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView). Ad esempio, le schermate seguenti mostrano un `CollectionView` che usa una griglia verticale a due colonne e che consente la selezione multipla:

[ ![Screenshot di un layout di griglia verticale di CollectionView, nel layout della griglia verticale di iOS e Android](introduction-images/verticalgrid-multipleselection.png "CollectionView con selezione multipla") ] (introduction-images/verticalgrid-multipleselection-large.png#lightbox "Layout griglia verticale di CollectionView con selezione multipla")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)è disponibile in Novell. Forms 4,0. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la seguente riga di codice alla `AppDelegate` classe in iOS o `MainActivity` alla classe in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)è disponibile in iOS e Android, ma è disponibile solo parzialmente nella piattaforma UWP (Universal Windows Platform).

## <a name="collectionview-and-listview-differences"></a>Differenze di CollectionView e ListView

Sebbene le [`CollectionView`](xref:Xamarin.Forms.CollectionView) API [`ListView`](xref:Xamarin.Forms.ListView) e siano simili, esistono alcune differenze rilevanti:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)dispone di un modello di layout flessibile che consente di presentare i dati verticalmente o orizzontalmente in un elenco o in una griglia.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta selezioni singole e multiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)non ha un concetto di celle. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)utilizza automaticamente la virtualizzazione fornita dai controlli nativi sottostanti.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)riduce la superficie dell'API [`ListView`](xref:Xamarin.Forms.ListView)di. Molte proprietà ed eventi di [`ListView`](xref:Xamarin.Forms.ListView) non sono presenti in `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)non include separatori predefiniti.

## <a name="move-from-listview-to-collectionview"></a>Passa da ListView a CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)è possibile eseguire la migrazione delle implementazioni nelle implementazioni esistenti di Novell [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Forms alle implementazioni con la seguente tabella:

| Concetto | API ListView | CollectionView |
|---|---|---|
| Data | `ItemsSource` | Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto viene popolato con i dati `ItemsSource` impostando la proprietà. Per altre informazioni, vedere [popolare un oggetto CollectionView con i dati](populate-data.md#populate-a-collectionview-with-data). |
| Aspetto elemento | `ItemTemplate` | L'aspetto di ogni elemento in un [`CollectionView`](xref:Xamarin.Forms.CollectionView) oggetto può essere definito impostando `ItemTemplate` la proprietà su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)un oggetto. Per altre informazioni, vedere [definire l'aspetto dell'elemento](populate-data.md#define-item-appearance). |
| Celle | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)non ha un concetto di celle. Viene invece utilizzato un modello di dati per definire l'aspetto di ogni elemento di dati nell'elenco. |
| Separatori di riga | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)non include separatori predefiniti. Se necessario, è possibile fornire tali elementi nel modello di elemento. |
| Selection | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)supporta selezioni singole e multiple. Per ulteriori informazioni, vedere [Novell. Forms CollectionView Selection](selection.md). |
| Altezza riga | `HasUnevenRows`, `RowHeight` | In, l'altezza della riga di ogni elemento è determinata `ItemSizingStrategy` dalla proprietà. `CollectionView` Per ulteriori informazioni, vedere [ridimensionamento di elementi](layout.md#item-sizing).|
| Memorizzazione nella cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)Usa automaticamente la virtualizzazione fornita dai controlli nativi sottostanti. |
| Intestazioni e piè di pagina | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)può presentare un'intestazione e un piè di pagina che scorrono con gli elementi dell'elenco, tramite `Header`le `Footer`proprietà `HeaderTemplate`,, `FooterTemplate` e. Per ulteriori informazioni, vedere [intestazioni e piè](layout.md#headers-and-footers)di pagina. |
| Raggruppamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)Visualizza i dati raggruppati correttamente impostando `IsGrouped` la relativa `true`proprietà su. Le intestazioni di gruppo e i piè di pagina di gruppo possono `GroupHeaderTemplate` essere `GroupFooterTemplate` personalizzati impostando le proprietà e su [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) oggetti. Per ulteriori informazioni, vedere [raggruppamento di Novell. Forms CollectionView](grouping.md). |
| Pull per aggiornare | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Il pull per l'aggiornamento non è attualmente supportato `CollectionView`in, ma verrà aggiunto in una versione futura. |
| Azioni di contesto | `ContextActions` | Le azioni di contesto non sono attualmente supportate `CollectionView`in, ma verranno aggiunte in una versione futura. |
| Scorrimento | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)definisce `ScrollTo` i metodi che scorrono gli elementi nella visualizzazione. Per ulteriori informazioni, vedere [scorrimento](scrolling.md). |

## <a name="related-links"></a>Collegamenti correlati

- [CollectionView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
