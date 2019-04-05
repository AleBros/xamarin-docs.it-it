---
title: Xamarin. Forms ListView
description: Questa guida presenta il ListView xamarin. Forms, che può essere usato per presentare i dati negli elenchi belle e interattivi.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: 540e787b476fc2b249bfbe487e45ac61f2e7405a
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329351"
---
# <a name="xamarinforms-listview"></a>Xamarin. Forms ListView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/WorkingWithListview)

[`ListView`](xref:Xamarin.Forms.ListView) è una vista per presentare elenchi di dati, in particolare lunghi elenchi che richiedono lo scorrimento.

> [!IMPORTANT]
> `CollectionView` è una vista per presentare elenchi di dati usando le specifiche di un layout diverso. L'obiettivo è fornire una più flessibile ed efficace alternativa per [ `ListView` ](xref:Xamarin.Forms.ListView). Per altre informazioni, vedere [xamarin. Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="use-cases"></a>Casi d'uso

Assicurarsi che ListView è il controllo a destra per le proprie esigenze. ListView può essere utilizzato in qualsiasi situazione in cui si intende visualizzare elenchi scorrevoli di dati. ListView che supporta azioni di contesto e il data binding.

ListView non deve essere confuso con [TableView](~/xamarin-forms/user-interface/tableview.md). Il controllo TableView è un'opzione migliore se si dispone di un elenco non associata di opzioni o i dati. Ad esempio, l'app impostazioni iOS che dispone di un set di opzioni per lo più predefinito, è più adatto da usare TableView rispetto a ListView.

Si noti che è consigliabile un ListView applicabile anche per i dati omogenei &ndash; , ovvero tutti i dati devono essere dello stesso tipo. Questo avviene perché solo un tipo di cella può essere usato per ogni riga nell'elenco. TableViews può supportare più tipi di cella, in modo che siano un'opzione migliore quando è necessario combinare le viste.

## <a name="components"></a>Componenti
ListView è un numero di componenti disponibili per applicare la funzionalità native di ogni piattaforma. Ognuno di questi componenti è il seguente:

- **[Informazioni sulle intestazioni e piè di pagina](customizing-list-appearance.md#Headers_and_Footers)**  &ndash; testo o vista da visualizzare all'inizio e alla fine di un elenco separati dai dati dell'elenco. Informazioni sulle intestazioni e piè di pagina può essere associato a un'origine dati in modo indipendente dall'origine dati del ListView.
- **[I gruppi](customizing-list-appearance.md#Grouping)**  &ndash; dati in un ListView possono essere raggruppati per semplificare l'esplorazione. I gruppi sono in genere associati a dati:

![](images/grouping-depth.png "ListView con dati raggruppati")

- **[Le celle](customizing-cell-appearance.md)**  &ndash; nelle celle di presentazione dei dati in un ListView. Ogni cella corrisponde a una riga di dati. Sono presenti celle predefinite per scegliere da, o è possibile definire il proprio cella personalizzata. Le celle incorporate e personalizzate possono essere usati/definito in XAML o codice.
  - **[Incorporati](customizing-cell-appearance.md#Built_in_Cells)**  &ndash; compilati nelle celle, in particolare TextCell e ImageCell, può risultare molto utile per le prestazioni, poiché corrispondono ai controlli nativi in ciascuna piattaforma.
       - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash; Visualizza una stringa di testo, facoltativamente con testo dettagliato. Testo dei dettagli viene visualizzato come una seconda riga in un carattere più piccolo con un colore.
       - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash; Visualizza un'immagine con il testo. Viene visualizzato come un TextCell con un'immagine a sinistra.
  - **[Le celle personalizzate](customizing-cell-appearance.md#customcells)**  &ndash; celle personalizzata sono molto utili se è necessario per presentare i dati complessi. Ad esempio, una visualizzazione personalizzata può essere usata per presentare un elenco di brani, tra cui album e artista:

![](images/image-cell-default.png "ListView con ImageCells")

Per altre informazioni sulla personalizzazione di celle in un ListView, vedere [personalizzazione di aspetto delle celle ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funzionalità
ListView supporta un numero di stili di interazione, tra cui:

- **[Pull per aggiornare](interactivity.md#Pull_to_Refresh)**  &ndash; ListView supporta pull per aggiornare in ogni piattaforma.
- **[Azioni di contesto](interactivity.md#Context_Actions)**  &ndash; ListView supporta intraprendere azioni per singoli elementi in un elenco. Ad esempio, è possibile implementare scorrere all'azione in iOS o azioni in Android toccare a lungo.
- **[Selezione](interactivity.md#selectiontaps)**  &ndash; può restare in ascolto per le selezioni e deselezioni non a intervenire quando viene toccata una riga.

![](images/context-default.png "ListView con azioni di contesto")

Per altre informazioni sulle funzionalità di interattività di ListView, vedere [azioni & interattività con ListView](interactivity.md).

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo con ListView (esempio)](https://developer.xamarin.com/samples/WorkingWithListview)
- [Associazione bidirezionale (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [Compilato In celle (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Celle personalizzate (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Raggruppamento (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [Visualizzazione di Renderer personalizzato (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/WorkingWithListviewNative)
- [Interattività ListView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
