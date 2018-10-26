---
title: Funzionalità e le parti di ListView
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: 248baa5daceff6db01098a155600ea204547e845
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116114"
---
# <a name="listview-parts-and-functionality"></a>Funzionalità e le parti di ListView


## <a name="overview"></a>Panoramica

Oggetto `ListView` costituita dalle parti seguenti:

- **Righe** &ndash; la rappresentazione visibile di dati nell'elenco.

- **Adapter** &ndash; una classe non visivo che associa l'origine dati alla visualizzazione elenco.

- **Scorrimento rapido** &ndash; un handle che consente all'utente di scorrere la lunghezza dell'elenco.

- **Sezione indice** &ndash; righe di un elemento dell'interfaccia utente mobile rispetto allo scorrimento per indicare dove nell'elenco di righe corrente si trovano.

Queste schermate usano una basic `ListView` controllo per mostrare la modalità di rendering lo scorrimento veloce e l'indice di sezioni:

[![Alcune schermate dell'App con le righe precedenti semplice, veloce lo scorrimento e l'indice di sezioni](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Gli elementi che costituiscono un `ListView` sono descritti in dettaglio più avanti:


## <a name="rows"></a>Righe

Ogni riga ha il proprio `View`. La vista può essere una delle visualizzazioni predefinite definite in `Android.Resources`, o una visualizzazione personalizzata. Ogni riga è possibile usare lo stesso layout di visualizzazione o possono essere tutti diversi. Sono disponibili esempi in questo documento dell'uso di layout predefinite e altri che spiega come definire i layout personalizzati.


## <a name="adapter"></a>Adattatore

Il `ListView` controllo richiede un `Adapter` fornire formattato `View` per ogni riga. Android ha incorporato schede e viste che possono essere utilizzate o è possibile creare classi personalizzate.


## <a name="fast-scrolling"></a>Lo scorrimento veloce

Quando un `ListView` contiene molte righe di dati lo scorrimento rapido può essere abilitato per consentire all'utente di passare a qualsiasi parte dell'elenco. Fast-allo scorrimento 'barra di scorrimento' può essere facoltativamente abilitato (e personalizzate nel livello API 11 e versioni successive).


## <a name="section-index"></a>Indice di sezioni

Durante lo scorrimento di lunghi elenchi, l'indice di sezioni facoltative fornisce all'utente con commenti e suggerimenti su quale parte dell'elenco si sta visualizzando. È solo appropriato in lunghi elenchi, in genere in combinazione con scorrimento rapido.


## <a name="classes-overview"></a>Cenni preliminari sulle classi

Le classi primarie utilizzate per visualizzare `ListViews` riportati di seguito:

[![UML-Diagramma che illustra le relazioni tra ListView e classi associate](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

Lo scopo di ogni classe è il seguente:

- **ListView** &ndash; elemento dell'interfaccia utente che visualizza una raccolta di righe scorrevole. Su telefoni, in genere utilizza tutta l'intera schermata (nel qual caso il `ListActivity` classe può essere utilizzata) o è stato possibile far parte di un layout più grande in dispositivi tablet o telefoni.

- **Vista** &ndash; una visualizzazione in Android può essere qualsiasi elemento dell'interfaccia utente, ma nel contesto di un `ListView` richiede una `View` da fornire per ogni riga.

- **BaseAdapter** &ndash; classe di Base per implementazioni dell'adattatore associare un `ListView` a un'origine dati.

- **ArrayAdapter** &ndash; classe dell'adattatore predefinito che associa una matrice di stringhe in cui un `ListView` per la visualizzazione. Il tipo generico `ArrayAdapter<T>` esegue la stessa operazione per altri tipi.

- **CursorAdapter** &ndash; uso `CursorAdapter` o `SimpleCursorAdapter` per visualizzare i dati in base a una query SQLite.

Questo documento contiene semplici esempi che usano un `ArrayAdapter` oltre a esempi più complessi che richiedono le implementazioni personalizzate degli `BaseAdapter` o `CursorAdapter`.

