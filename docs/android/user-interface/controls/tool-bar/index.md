---
title: ToolBar
description: "La barra degli strumenti è un componente di barra di azione che garantisce una maggiore flessibilità rispetto a barra delle azioni predefinite: può essere collocato ovunque nell'app, la dimensione può essere modificata e può utilizzare una combinazione di colori che è diversa dal tema dell'app. Inoltre, ogni schermata dell'app può avere più barre degli strumenti."
ms.topic: article
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 30b1cb280c2817f55d73e10ff8b4d7942011bf2c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="toolbar"></a>ToolBar

_La barra degli strumenti è un componente di barra di azione che garantisce una maggiore flessibilità rispetto a barra delle azioni predefinite: può essere collocato ovunque nell'app, la dimensione può essere modificata e può utilizzare una combinazione di colori che è diversa dal tema dell'app. Inoltre, ogni schermata dell'app può avere più barre degli strumenti._

 
## <a name="overview"></a>Panoramica

Un elemento chiave della progettazione di qualsiasi attività Android è un *barra delle azioni*. La barra delle azioni è il componente dell'interfaccia utente che viene utilizzato per la navigazione, ricerca, i menu e personalizzazione in un'app per Android. Nelle versioni Android prima del simbolo Android 5.0, la barra delle azioni (noto anche come il *barra app*) è il componente consigliato per fornire questa funzionalità. 

Il `Toolbar` widget, introdotto in Android 5.0 simbolo, può essere considerato come una generalizzazione dell'interfaccia barra azione &ndash; è destinato a sostituire la barra delle azioni. Il `Toolbar` possono essere utilizzate ovunque in un layout dell'app ed è molto più personalizzabile rispetto a una barra di azione. Nella schermata seguente viene illustrata la versione personalizzata `Toolbar` esempio creato in questa guida: 

[![Schermata di esempio di una barra degli strumenti con modificare, salvare e voci di menu di overflow](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Esistono alcune importanti differenze tra il `Toolbar` e la barra delle azioni: 

-   Oggetto `Toolbar` può essere collocato ovunque nell'interfaccia utente.

-   Più barre degli strumenti possono essere visualizzati nella schermata stessa.

-   Se si utilizzano i frammenti, ogni frammento può avere un proprio `Toolbar`. 

-   Oggetto `Toolbar` può essere configurato per coprire solo una parziale larghezza dello schermo. 

-   Poiché il `Toolbar` non è associata alla combinazione di colori di progettazione finestra dell'attività, può avere una combinazione di colori visivamente distinto. 

-   A differenza della barra delle azioni, di `Toolbar` non include un'icona a sinistra. I menu a destra utilizzano meno spazio. 

-   Il `Toolbar` altezza è regolabile. 

-   Altre viste possono essere inclusi all'interno di `Toolbar`. 

Oggetto `Toolbar` può contenere uno o più dei seguenti elementi: 

-   Pulsante di spostamento

-   Un'immagine del logo personalizzato

-   Titolo e sottotitolo

-   Visualizzazioni personalizzate

-   Dal menu azione

-   Menu di overflow

Google [linee guida di progettazione di materiale](https://material.google.com/) consiglia di sfruttare i vantaggi di questi elementi per assegnare le app un aspetto distinto (anziché basarsi esclusivamente su un'icona dell'applicazione e il titolo). 

Questa guida illustra l'uso frequente `Toolbar` scenari:

-   Sostituzione sulla barra di azione predefinito di un'attività con un `Toolbar`. 

-   L'aggiunta di un secondo `Toolbar` a un'attività.

-   Utilizzando il **delle applicazioni di libreria di supporto Android v7** libreria (detto *delle applicazioni* nella parte restante di questa Guida) per distribuire `Toolbar` nelle versioni precedenti di Android. 

 
 
## <a name="requirements"></a>Requisiti

`Toolbar` è disponibile in Android 5.0 simbolo (API 21) e versioni successive. Quando si rilascia destinazione Android precedenti alla Android 5.0, utilizzare il [delle applicazioni di libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), che fornisce compatibilità `Toolbar` supporto in un pacchetto NuGet. 
[Compatibilità degli strumenti](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) viene illustrato come utilizzare questa raccolta. 




## <a name="related-links"></a>Collegamenti correlati

- [Il simbolo della barra degli strumenti (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti delle applicazioni (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
