---
title: ToolBar
description: "La barra degli strumenti è un componente della barra delle azioni che offre una maggiore flessibilità rispetto alla barra delle azioni predefinita: può essere inserita in un punto qualsiasi dell'app, la relativa dimensione può essere modificata e può usare una combinazione di colori diversa da quella del tema dell'app. Ogni schermata dell'app può inoltre avere più barre degli strumenti."
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 3f4a9393d8ef6ef54ba3dba29f1109080f1e321a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029099"
---
# <a name="toolbar"></a>ToolBar

_La barra degli strumenti è un componente della barra delle azioni che offre una maggiore flessibilità rispetto alla barra delle azioni predefinita: può essere inserita in un punto qualsiasi dell'app, la relativa dimensione può essere modificata e può usare una combinazione di colori diversa da quella del tema dell'app. Ogni schermata dell'app può inoltre avere più barre degli strumenti._

## <a name="overview"></a>Panoramica

Un elemento di progettazione chiave di qualsiasi attività Android è una *barra delle azioni*. La barra delle azioni è il componente dell'interfaccia utente usato per la navigazione, la ricerca, i menu e la personalizzazione in un'app per Android. Nelle versioni di Android prima di Android 5,0 Lollipop la barra delle azioni (nota anche come *barra dell'app*) era il componente consigliato per fornire questa funzionalità. 

Il widget `Toolbar` (introdotto in Android 5,0 Lollipop) può essere considerato come una generalizzazione dell'interfaccia della barra delle azioni &ndash; si intende sostituire la barra delle azioni. Il `Toolbar` può essere usato in qualsiasi punto del layout dell'app ed è molto più personalizzabile rispetto a una barra delle azioni. Lo screenshot seguente illustra l'esempio personalizzato `Toolbar` creato in questa guida: 

[![screenshot di esempio di una barra degli strumenti con voci di menu modifica, Salva e overflow](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Esistono alcune differenze importanti tra il `Toolbar` e la barra delle azioni: 

- Un `Toolbar` può essere inserito in un punto qualsiasi dell'interfaccia utente.

- Nella stessa schermata è possibile visualizzare più barre degli strumenti.

- Se vengono usati i frammenti, ogni frammento può avere un proprio `Toolbar`. 

- Una `Toolbar` può essere configurata in modo da estendersi solo a una larghezza parziale dello schermo. 

- Poiché il `Toolbar` non è associato alla combinazione di colori della decorazione della finestra dell'attività, può disporre di una combinazione di colori visivamente distinta. 

- Diversamente dalla barra delle azioni, il `Toolbar` non include un'icona a sinistra. I menu a destra utilizzano meno spazio. 

- L'altezza del `Toolbar` è regolabile. 

- È possibile includere altre visualizzazioni all'interno del `Toolbar`. 

Un `Toolbar` può contenere uno o più degli elementi seguenti: 

- Pulsante di spostamento

- Immagine logo personalizzata

- Titolo e sottotitolo

- Visualizzazioni personalizzate

- Menu azione

- Menu di overflow

Le [linee guida](https://material.google.com/) per la progettazione di materiali di Google consigliano di sfruttare questi elementi per offrire alle app un aspetto distinto, invece di basarsi esclusivamente sull'icona e sul titolo di un'applicazione. 

In questa guida vengono illustrati gli scenari di `Toolbar` utilizzati più di frequente:

- Sostituzione della barra delle azioni predefinita di un'attività con un `Toolbar`. 

- Aggiunta di un secondo `Toolbar` a un'attività.

- Usando la libreria di **supporto di Android V7 AppCompat** Library (denominata *AppCompat* nella parte restante di questa guida) per distribuire `Toolbar` nelle versioni precedenti di Android. 

## <a name="requirements"></a>Requisiti

`Toolbar` è disponibile in Android 5,0 Lollipop (API 21) e versioni successive. Quando la destinazione è costituita da versioni di Android precedenti a Android 5,0, usare la [libreria di supporto di Android V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), che fornisce il supporto `Toolbar` compatibile con le versioni precedenti in un pacchetto NuGet. 
La [compatibilità della barra degli strumenti](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) spiega come usare questa libreria. 

## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti Lollipop (Sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra degli strumenti AppCompat (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
