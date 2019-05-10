---
title: Introduzione a xamarin. Forms CollectionView
description: La visualizzazione di raccolta è una visualizzazione flessibile ed efficiente per presentare elenchi di dati usando le specifiche di un layout diverso.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 23de838a395b68f656f95c5665dc97a1d928bece
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054651"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introduzione a xamarin. Forms CollectionView

![](~/media/shared/preview.png "Questa API è ancora in versione definitiva")

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` è una vista per presentare elenchi di dati usando le specifiche di un layout diverso. L'obiettivo è fornire una più flessibile ed efficace alternativa per [ `ListView` ](xref:Xamarin.Forms.ListView). Ad esempio, le schermate seguenti illustrano un `CollectionView` che utilizza una griglia verticale a due colonne e che consente la selezione multipla:

[![Screenshot di un layout di griglia verticali CollectionView, su iOS e Android](introduction-images/verticalgrid-multipleselection.png "layout di griglia verticali CollectionView con selezione multipla")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "layout di griglia verticali CollectionView con selezione multipla")

`CollectionView` è disponibile nelle pre-versioni 4.0 di xamarin. Forms. Tuttavia, è attualmente in fase sperimentale e può essere usato solo aggiungendo la riga seguente di codice per il `AppDelegate` classe in iOS o alle `MainActivity` classe in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> `CollectionView` è disponibile solo in iOS e Android.

## <a name="collectionview-and-listview-differences"></a>Differenze di visualizzazione di raccolta e ListView

Mentre il `CollectionView` e `ListView` sono simili, esistono alcune differenze significative:

- `CollectionView` dispone di un modello di layout flessibile che consente di essere visualizzato orizzontalmente o verticalmente, in un elenco o una griglia dati.
- `CollectionView` supporta singolo e selezione multipla.
- `CollectionView` è presente alcun concetto di celle. Al contrario, un modello di dati consente di definire l'aspetto di ogni elemento di dati nell'elenco.
- `CollectionView` utilizza automaticamente sulla virtualizzazione fornita dai controlli nativi sottostanti.
- `CollectionView` Consente di ridurre la superficie dell'API [ `ListView` ](xref:Xamarin.Forms.ListView). Molte proprietà e gli eventi da `ListView` non sono presenti nel `CollectionView`.
- `CollectionView` non sono inclusi i separatori incorporati.

## <a name="move-from-listview-to-collectionview"></a>Spostare da ListView alla visualizzazione di raccolta

[`ListView`](xref:Xamarin.Forms.ListView) le implementazioni nelle implementazioni esistenti di xamarin. Forms possono essere migrate a `CollectionView` implementazioni con l'aiuto della tabella seguente:

| Concetto | API di ListView | CollectionView |
|---|---|---|
| Dati | `ItemsSource` | Oggetto `CollectionView` viene popolata con i dati tramite l'impostazione relativa `ItemsSource` proprietà. Per altre informazioni, vedere [popolare una visualizzazione di raccolta dati](populate-data.md#populate-a-collectionview-with-data). |
| Aspetto dell'elemento | `ItemTemplate` | L'aspetto di ogni elemento in un `CollectionView` può essere definita impostando la `ItemTemplate` proprietà su un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Per altre informazioni, vedere [definiscono l'aspetto dell'elemento](populate-data.md#define-item-appearance). |
| Celle | `TextCell`, `ImageCell`, `ViewCell` | `CollectionView` è presente alcun concetto di celle. Al contrario, un modello di dati consente di definire l'aspetto di ogni elemento di dati nell'elenco. |
| Separatori di riga | `SeparatorColor`, `SeparatorVisibility` | `CollectionView` non sono inclusi i separatori incorporati. Questi possono essere forniti, se si desidera, nel modello di elemento. |
| Selection | `SelectionMode`, `SelectedItem` | `CollectionView` supporta singolo e selezione multipla. Per altre informazioni, vedere [selezione di xamarin. Forms CollectionView](selection.md). |
| Altezza di riga | `HasUnevenRows`, `RowHeight` | In un `CollectionView`, l'altezza della riga di ciascun elemento è determinato dal `ItemSizingStrategy` proprietà. Per altre informazioni, vedere [ridimensionamento elemento](layout.md#item-sizing).|
| Memorizzazione nella cache | `CachingStrategy` | `CollectionView` Usa automaticamente la virtualizzazione fornita dai controlli nativi sottostanti. |
| Informazioni sulle intestazioni e piè di pagina | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | Informazioni sulle intestazioni e piè di pagina non sono attualmente supportati `CollectionView`, ma verrà aggiunto nelle versioni future.|
| Raggruppamento | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | Raggruppamento non è supportato in `CollectionView`, ma verrà aggiunto nelle versioni future. |
| Pull da aggiornare | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Pull da aggiornare non è supportata in `CollectionView`, ma verrà aggiunto nelle versioni future. |
| Azioni di contesto | `ContextActions` | Azioni di contesto non sono attualmente supportate `CollectionView`, ma verrà aggiunto nelle versioni future. |
| Scorrimento | `ScrollTo()` | `CollectionView` definisce `ScrollTo` metodi, scorrere gli elementi all'interno della visualizzazione. Per altre informazioni, vedere [Scrolling](scrolling.md). |

## <a name="related-links"></a>Collegamenti correlati

- [Visualizzazione di raccolta (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
