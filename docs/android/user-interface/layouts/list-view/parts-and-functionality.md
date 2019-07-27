---
title: Parti e funzionalità di ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 3ab7a923dabd6b98c509870abaa51b12fb63c8d2
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510129"
---
# <a name="xamarinandroid-listview-parts-and-functionality"></a>Parti e funzionalità di ListView di Novell. Android

Un `ListView` è costituito dalle parti seguenti:

- **Righe** &ndash; Rappresentazione visibile dei dati nell'elenco.

- **Scheda** &ndash; Classe non visiva che associa l'origine dati alla visualizzazione elenco.

- **Scorrimento rapido** &ndash; Handle che consente all'utente di scorrere la lunghezza dell'elenco.

- **Indice della sezione** &ndash; Elemento dell'interfaccia utente che esegue il float sulle righe di scorrimento per indicare la posizione dell'elenco in cui si trovano le righe correnti.

Queste schermate utilizzano un controllo `ListView` di base per mostrare il rendering dello scorrimento rapido e dell'indice della sezione:

[![Screenshot delle app che usano semplici righe, scorrimento rapido e indice delle sezioni](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Gli elementi che costituiscono un oggetto `ListView` sono descritti in dettaglio di seguito:


## <a name="rows"></a>Righe

Ogni riga ha un proprio `View`. La vista può essere una delle viste predefinite definite in `Android.Resources`o una visualizzazione personalizzata. Ogni riga può utilizzare lo stesso layout di visualizzazione oppure può essere diversa. In questo documento vengono illustrati alcuni esempi di utilizzo di layout predefiniti e altri che spiegano come definire layout personalizzati.


## <a name="adapter"></a>Adattatore

Il `ListView` controllo richiede un `Adapter` oggetto per fornire il `View` formattato per ogni riga. Android include schede e visualizzazioni predefinite che possono essere usate oppure è possibile creare classi personalizzate.


## <a name="fast-scrolling"></a>Scorrimento rapido

Quando una `ListView` contiene molte righe di scorrimento rapido dei dati può essere abilitata per consentire all'utente di passare a qualsiasi parte dell'elenco. La barra di scorrimento a scorrimento rapido può essere abilitata facoltativamente e personalizzata nel livello API 11 e versioni successive.


## <a name="section-index"></a>Indice della sezione

Durante lo scorrimento degli elenchi lunghi, l'indice della sezione facoltativa fornisce all'utente commenti e suggerimenti sulla parte dell'elenco attualmente visualizzata. È appropriato solo per gli elenchi lunghi, in genere in combinazione con lo scorrimento rapido.


## <a name="classes-overview"></a>Cenni preliminari sulle classi

Le classi primarie usate per `ListViews` la visualizzazione sono illustrate di seguito:

[![Diagramma UML che illustra le relazioni tra ListView e le classi associate](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

Lo scopo di ogni classe è descritto di seguito:

- **ListView** &ndash; elemento dell'interfaccia utente che visualizza una raccolta di righe scorrevole. Nei telefoni viene in genere utilizzata l'intera schermata (in tal caso, è `ListActivity` possibile utilizzare la classe) o potrebbe far parte di un layout più grande nei telefoni o nei dispositivi tablet.

- **Visualizza** una vista in Android può essere qualsiasi elemento dell'interfaccia utente, ma nel contesto di un `ListView` è necessario specificare `View` un oggetto per ogni riga. &ndash;

- **BaseAdapter** Classe base per le implementazioni dell'adapter per `ListView` associare un oggetto a un'origine dati. &ndash;

- **Classe ArrayAdapter** Classe adattatori incorporati che associa una matrice di stringhe a un oggetto `ListView` per la visualizzazione. &ndash; L'oggetto `ArrayAdapter<T>` generico è identico per altri tipi.

- **CursorAdapter** &ndash; Usare oper`SimpleCursorAdapter` visualizzare i dati in base a una query SQLite. `CursorAdapter`

Questo documento contiene semplici esempi che usano un `ArrayAdapter` insieme ad esempi più complessi che richiedono implementazioni personalizzate di `BaseAdapter` o `CursorAdapter`.

