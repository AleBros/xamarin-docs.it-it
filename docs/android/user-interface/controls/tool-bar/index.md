---
title: ToolBar
description: Barra degli strumenti è un componente barra di azione che offre maggiore flessibilità rispetto a barra delle azioni predefinita:, può essere inserito in un punto qualsiasi nell'app, la dimensione può essere modificata e può usare una combinazione di colori che è diversa dal tema dell'app. Inoltre, ogni schermata dell'app può avere più barre degli strumenti.
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 2c9de4058fdaee53671e65f49ad95c3af5e127d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082832"
---
# <a name="toolbar"></a>ToolBar

_Barra degli strumenti è un componente barra di azione che offre maggiore flessibilità rispetto a barra delle azioni predefinita:, può essere inserito in un punto qualsiasi nell'app, la dimensione può essere modificata e può usare una combinazione di colori che è diversa dal tema dell'app. Inoltre, ogni schermata dell'app può avere più barre degli strumenti._

 
## <a name="overview"></a>Panoramica

Un elemento chiave nella progettazione di qualsiasi attività Android è un *sulla barra delle azioni*. Barra delle azioni è il componente dell'interfaccia utente che è utilizzato per la navigazione, ricerca, i menu e personalizzazione in un'app per Android. Nelle versioni di Android precedenti alla Android 5.0 Lollipop, barra delle azioni (noto anche come il *barra dell'app*) è il componente consigliato per fornire questa funzionalità. 

Il `Toolbar` widget (introdotta in Android 5.0 Lollipop) può essere considerato una generalizzazione dell'interfaccia barra azione &ndash; destinato a sostituire la barra delle azioni. Il `Toolbar` possono essere utilizzati ovunque in un layout dell'app ed è molto più personalizzabile rispetto a una barra delle azioni. Lo screenshot seguente illustra la versione personalizzata `Toolbar` esempio creata in questa guida: 

[![Screenshot di esempio di una barra degli strumenti modifica, salvare e voci di menu di overflow](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Esistono alcune importanti differenze tra la `Toolbar` e barra delle azioni: 

-   Oggetto `Toolbar` può essere inserito in un punto qualsiasi nell'interfaccia utente.

-   Più barre degli strumenti possono essere visualizzati nella stessa schermata.

-   Se si usano frammenti, ogni frammento può avere un proprio `Toolbar`. 

-   Oggetto `Toolbar` può essere configurato per estendersi solo una parziale larghezza dello schermo. 

-   Poiché il `Toolbar` non è associata alla combinazione di colori di decor finestra dell'attività, può avere una combinazione di colori visivamente distinte. 

-   A differenza di barra delle azioni di `Toolbar` non include un'icona visualizzata a sinistra. Menu a destra utilizzare meno spazio. 

-   Il `Toolbar` altezza è regolabile. 

-   Possono essere incluse altre visualizzazioni all'interno di `Toolbar`. 

Oggetto `Toolbar` può contenere uno o più dei seguenti elementi: 

-   Pulsante di spostamento

-   Un'immagine del logo personalizzato

-   Titolo e sottotitolo

-   Visualizzazioni personalizzate

-   Menu azione

-   Menu di overflow

Google [Material Design guidelines](https://material.google.com/) consiglia sfruttando i vantaggi di questi elementi per assegnare le app un aspetto distinto (anziché fare affidamento esclusivamente su un'icona dell'applicazione e il titolo). 

Questa guida illustra l'uso più comune `Toolbar` scenari:

-   La sostituzione sulla barra delle azioni predefinita dell'attività con un `Toolbar`. 

-   Aggiunta di un secondo `Toolbar` a un'attività.

-   Usando il **libreria di supporto Android v7 AppCompat** library (detto *AppCompat* nella parte restante di questa Guida) per distribuire `Toolbar` nelle versioni precedenti di Android. 

 
 
## <a name="requirements"></a>Requisiti

`Toolbar` è disponibile in Android 5.0 Lollipop (API 21) e versioni successive. Quando si rilascia come destinazione di Android precedente alla versione 5.0 di Android, usare il [libreria di supporto Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), che offre compatibilità `Toolbar` supportare in un pacchetto NuGet. 
[Compatibilità di ToolBar](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) spiega come usare questa libreria. 




## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti lollipop (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti AppCompat (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
