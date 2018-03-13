---
title: "Funzionalità e le parti di ListView"
ms.topic: article
ms.prod: xamarin
ms.assetid: ABA40FED-FF68-C0B0-BC43-C748CEE585D8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 4a7947c40d80c0ff8cb35dab54a11907280335d9
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="listview-parts-and-functionality"></a>Funzionalità e le parti di ListView


## <a name="overview"></a>Panoramica

Oggetto `ListView` costituita dalle parti seguenti:

- **Righe** &ndash; la rappresentazione dei dati nell'elenco visibile.

- **Scheda** &ndash; una classe non visivi che associa l'origine dati alla visualizzazione elenco.

- **Scorrimento rapido** &ndash; un handle che consente all'utente di scorrere la lunghezza dell'elenco.

- **Sezione indice** &ndash; le righe di un elemento dell'interfaccia utente che può essere spostata sulle barre di scorrimento per indicare dove nell'elenco di righe corrente si trovano.

Queste schermate usano un basic `ListView` controllo per mostrare la modalità di rendering scorrimento rapido e l'indice di sezioni:

[![Schermate dell'App con le righe meno recenti semplice, veloce lo scorrimento e l'indice di sezioni](parts-and-functionality-images/listviewparts.png)](parts-and-functionality-images/listviewparts.png#lightbox)

Gli elementi che costituiscono un `ListView` sono descritti in dettaglio più avanti:


## <a name="rows"></a>Righe

Ogni riga ha il proprio `View`. La vista può essere una delle visualizzazioni predefinite definite `Android.Resources`, o una vista personalizzata. Ogni riga è possibile utilizzare lo stesso layout visualizzazione o possono essere tutti diversi. Sono disponibili esempi in questo documento dell'utilizzo di layout predefinito e altri utenti che descrivono come definire un layout personalizzato.


## <a name="adapter"></a>Adattatore

Il `ListView` controllo richiede un `Adapter` per fornire il formato `View` per ogni riga. Android è incorporato schede e viste che possono essere utilizzate o è possibile creare classi personalizzate.


## <a name="fast-scrolling"></a>Scorrimento rapido

Quando un `ListView` contiene molte righe di dati lo scorrimento rapido può essere abilitato per consentire all'utente di passare a qualsiasi parte dell'elenco. Fast-allo scorrimento 'barra di scorrimento' può essere facoltativamente abilitato (e personalizzati nel livello API 11 e versioni successive).


## <a name="section-index"></a>Indice di sezioni

Durante lo scorrimento lunghi elenchi, l'indice della sezione facoltativa fornisce all'utente con commenti e suggerimenti su quale parte dell'elenco da cui sta attualmente visualizzando. È solo appropriato in lunghi elenchi, in genere in combinazione con scorrimento rapido.


## <a name="classes-overview"></a>Cenni preliminari sulle classi

Classi principali utilizzate per visualizzare `ListViews` riportati di seguito:

[![Diagramma UML che illustrano le relazioni tra ListView e classi associate](parts-and-functionality-images/image2.png)](parts-and-functionality-images/image2.png#lightbox)

Lo scopo di ogni classe è descritta di seguito:

- **ListView** &ndash; elemento dell'interfaccia utente che visualizza una raccolta di righe scorrevole. Su telefoni viene in genere utilizza tutta l'intero schermo (nel qual caso il `ListActivity` classe può essere utilizzata) o può far parte di un layout più grande su telefoni o dispositivi tablet.

- **Visualizzazione** &ndash; una visualizzazione in Android può essere qualsiasi elemento dell'interfaccia utente, ma nel contesto di un `ListView` richiede un `View` da fornire per ogni riga.

- **BaseAdapter** &ndash; classe di Base per implementazioni dell'adattatore associare un `ListView` a un'origine dati.

- **ArrayAdapter** &ndash; classe dell'adattatore predefinito che associa una matrice di stringhe per un `ListView` per la visualizzazione. Il tipo generico `ArrayAdapter<T>` esegue la stessa operazione per altri tipi.

- **CursorAdapter** &ndash; utilizzare `CursorAdapter` o `SimpleCursorAdapter` per visualizzare i dati in base a una query SQLite.

Questo documento contiene esempi semplici che usano un `ArrayAdapter` nonché esempi più complessi che richiedono le implementazioni personalizzate di `BaseAdapter` o `CursorAdapter`.

