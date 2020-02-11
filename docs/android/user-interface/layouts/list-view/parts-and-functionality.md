---
title: Parti e funzionalità di ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2017
ms.openlocfilehash: b8fd44a70f4c7ecdcf7919dec1c81461200b35bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028857"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Parti e funzionalità di ListView di Xamarin.Android

Una `ListView` è costituita dalle parti seguenti:

- **Righe** &ndash; rappresentazione visibile dei dati nell'elenco.

- L' **Adapter** &ndash; una classe non visiva che associa l'origine dati alla visualizzazione elenco.

- **Scorrimento rapido** &ndash; un handle che consente all'utente di scorrere la lunghezza dell'elenco.

- **Index Section** &ndash; un elemento dell'interfaccia utente che esegue il float sulle righe di scorrimento per indicare la posizione dell'elenco in cui si trovano le righe correnti.

Queste schermate usano un controllo `ListView` di base per mostrare il rendering dello scorrimento rapido e dell'indice della sezione:

[![screenshot delle app usando righe semplici, scorrimento rapido e indice delle sezioni](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Gli elementi che costituiscono un `ListView` vengono descritti in modo più dettagliato di seguito:

## <a name="rows"></a>Righe

Ogni riga dispone di un proprio `View`. La vista può essere una delle viste predefinite definite in `Android.Resources`o una vista personalizzata. Ogni riga può utilizzare lo stesso layout di visualizzazione oppure può essere diversa. In questo documento vengono illustrati alcuni esempi di utilizzo di layout predefiniti e altri che spiegano come definire layout personalizzati.

## <a name="adapter"></a>Adattatore

Il controllo `ListView` richiede che un `Adapter` fornisca il `View` formattato per ogni riga. Android include schede e visualizzazioni predefinite che possono essere usate oppure è possibile creare classi personalizzate.

## <a name="fast-scrolling"></a>Scorrimento rapido

Quando un `ListView` contiene molte righe di scorrimento rapido dei dati può essere abilitato per consentire all'utente di passare a qualsiasi parte dell'elenco. La barra di scorrimento a scorrimento rapido può essere abilitata facoltativamente e personalizzata nel livello API 11 e versioni successive.

## <a name="section-index"></a>Indice della sezione

Durante lo scorrimento degli elenchi lunghi, l'indice della sezione facoltativa fornisce all'utente commenti e suggerimenti sulla parte dell'elenco attualmente visualizzata. È appropriato solo per gli elenchi lunghi, in genere in combinazione con lo scorrimento rapido.

## <a name="classes-overview"></a>Cenni preliminari sulle classi

Di seguito sono illustrate le classi primarie usate per visualizzare `ListViews`:

[![diagramma UML che illustra le relazioni tra ListView e le classi associate](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

Lo scopo di ogni classe è descritto di seguito:

- **ListView** &ndash; elemento dell'interfaccia utente che visualizza una raccolta di righe scorrevole. Nei telefoni viene in genere utilizzato l'intero schermo (in questo caso, è possibile utilizzare la classe `ListActivity`) o potrebbe far parte di un layout più grande nei telefoni o nei dispositivi tablet.

- **Visualizzazione** &ndash; una vista in Android può essere qualsiasi elemento dell'interfaccia utente, ma nel contesto di un `ListView` è necessario fornire un `View` per ogni riga.

- **BaseAdapter** &ndash; classe di base per le implementazioni di adapter per associare una `ListView` a un'origine dati.

- **Classe arrayadapter** &ndash; classe di adattatori incorporata che associa una matrice di stringhe a un `ListView` per la visualizzazione. Il `ArrayAdapter<T>` generico esegue la stessa operazione per altri tipi.

- **CursorAdapter** &ndash; usare `CursorAdapter` o `SimpleCursorAdapter` per visualizzare i dati in base a una query SQLite.

Questo documento contiene semplici esempi che usano un `ArrayAdapter`, oltre ad esempi più complessi che richiedono implementazioni personalizzate di `BaseAdapter` o `CursorAdapter`.
