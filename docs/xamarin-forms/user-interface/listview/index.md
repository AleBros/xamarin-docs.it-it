---
title: ListView
description: Presentare i dati negli elenchi accattivanti e interattivi.
ms.prod: xamarin
ms.assetid: FEFDF7E0-720F-4BD1-863F-4477226AA695
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2015
ms.openlocfilehash: a153791893f99a472c3fcf91a205bf91ed971e13
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="listview"></a>ListView

ListView è una vista per la presentazione di elenchi di dati, particolarmente lunghi elenchi che richiedono lo scorrimento. Questa guida viene illustrato come utilizzare ListView:

1. **[Origini dati](data-and-databinding.md)**  &ndash; popolare un controllo ListView con dati, con o senza associazione a dati.
2. **[Aspetto delle celle](customizing-cell-appearance.md)**  &ndash; personalizzare l'aspetto delle celle predefinite o creare la propria cella personalizzato.
3. **[Elenco aspetto](customizing-list-appearance.md)**  &ndash; personalizzare l'aspetto del controllo ListView. Impostare le intestazioni e piè di pagina, abilitare i gruppi e modificare l'altezza delle righe.
4. **[Interattività](interactivity.md)**  &ndash; gestire le scelte e le selezioni, implementare pull per l'aggiornamento e aggiungere le azioni di scelta rapida.
5. **[Prestazioni](performance.md)**  &ndash; evitare problemi di prestazioni.

## <a name="use-cases"></a>Casi d'uso
Assicurarsi di che ListView è il controllo adatto alle proprie esigenze. ListView utilizzabile in qualsiasi situazione in cui si visualizzano elenchi scorrevoli di dati. Controlli ListView supporta azioni di contesto e l'associazione dati.

Controllo ListView non deve essere confuso con [TableView](~/xamarin-forms/user-interface/tableview.md). Il controllo di TableView è un'opzione migliore se si dispone di un elenco non associate di opzioni o i dati. Ad esempio, l'app impostazioni iOS, che ha un set di opzioni predefinito per lo più, è più adatto per utilizzare TableView di ListView.

Inoltre si noti che un controllo ListView è migliore adatto per i dati omogenei &ndash; , ovvero tutti i dati devono essere dello stesso tipo. In questo modo un solo tipo di cella può essere utilizzato per ogni riga nell'elenco. TableViews può supportare più tipi di cella, quindi sono un'opzione migliore se è necessario utilizzare più viste.


## <a name="components"></a>Componenti
ListView con un numero di componenti disponibili per verificare la funzionalità nativa di ogni piattaforma. Ognuno di questi componenti è descritta di seguito:

- **[Intestazioni e piè di pagina](customizing-list-appearance.md#Headers_and_Footers)**  &ndash; testo o vista da visualizzare all'inizio e alla fine di un elenco, separate dai dati dell'elenco. Intestazioni e piè di pagina può essere associato a un'origine dati in modo indipendente dall'origine dati del controllo ListView.
- **[Gruppi](customizing-list-appearance.md#Grouping)**  &ndash; dati in un controllo ListView possono essere raggruppati per semplificare l'esplorazione. I gruppi sono in genere associata ai dati:

![](images/grouping-depth.png "ListView con i dati raggruppati")

- **[Celle](customizing-cell-appearance.md)**  &ndash; nelle celle di presentazione dei dati in un controllo ListView. Ogni cella corrisponde a una riga di dati. Sono presenti celle predefinite da selezionare oppure è possibile definire la propria cella personalizzato. Le celle di incorporati e personalizzate possono essere utilizzati/definiti in XAML o nel codice.
  - **[Incorporata](customizing-cell-appearance.md#Built_in_Cells)**  &ndash; compilato nelle celle, in particolare TextCell e ImageCell, può essere ideale per le prestazioni, in quanto corrispondono ai controlli nativi in ciascuna piattaforma.
    - **[TextCell](customizing-cell-appearance.md#TextCell)**  &ndash; viene visualizzata una stringa di testo, facoltativamente con testo dettagliato. Testo dei dettagli viene visualizzato come una seconda riga in un carattere più piccolo con un colore.
    - **[ImageCell](customizing-cell-appearance.md#ImageCell)**  &ndash; Visualizza un'immagine con il testo. Viene visualizzato come un TextCell con un'immagine a sinistra.
  - **[Le celle personalizzate](customizing-cell-appearance.md#customcells)**  &ndash; celle personalizzati sono molto utili se è necessario presentare dati complessi. Ad esempio, una visualizzazione personalizzata può essere utilizzata per presentare un elenco di brani, tra cui album e artista:

![](images/image-cell-default.png "ListView con ImageCells")

Per ulteriori informazioni sulla personalizzazione di celle in un controllo ListView, vedere [personalizzazione aspetto delle celle ListView](customizing-cell-appearance.md).

## <a name="functionality"></a>Funzionalità
ListView supporta alcuni stili di interazione, tra cui:

- **[Pull per l'aggiornamento](interactivity.md#Pull_to_Refresh)**  &ndash; ListView supporta pull per l'aggiornamento in ogni piattaforma.
- **[Azioni del contesto](interactivity.md#Context_Actions)**  &ndash; ListView supporta un'azione per singoli elementi in un elenco. Ad esempio, è possibile implementare scorrere per l'operazione in iOS oppure toccare azioni in Android e Windows Phone.
- **[Selezione](interactivity.md#selectiontaps)**  &ndash; può restare in ascolto per le selezioni e deselezioni non intervenire quando verrà scelti una riga.

![](images/context-default.png "ListView con azioni di contesto")

Per ulteriori informazioni sulle caratteristiche di interattività di ListView, vedere [azioni & interattività con ListView](interactivity.md).


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo con ListView (esempio)](https://developer.xamarin.com/samples/WorkingWithListview)
- [Associazione bidirezionale (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/SwitchEntryTwoBinding)
- [Compilato In celle (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Celle personalizzate (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Raggruppamento (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [Visualizzazione di rendering personalizzata (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/WorkingWithListviewNative)
- [ListView interattività (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/interactivity)
- [iOS Workbook](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-ios.workbook)
- [Cartella di lavoro di Android](https://developer.xamarin.com/workbooks/xamarin-forms/user-interface/listview/ListView1-android.workbook)
