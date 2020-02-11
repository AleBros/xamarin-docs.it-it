---
title: Gerarchia di classi di controlli Xamarin.Forms
description: Gli sviluppatori devono conoscere la gerarchia dei tipi usati per creare l'interfaccia utente di un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
ms.openlocfilehash: 7c55ed3082a031352a8eefb8ef579060a9dd6404
ms.sourcegitcommit: 4899cf4aaa73aa56c48f7ee2339c0af8112e1feb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717300"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Gerarchia di classi di controlli Xamarin.Forms

Xamarin.Forms è costituito da centinaia di tipi, in più spazi dei nomi. Gli sviluppatori dovrebbero avere familiarità con la gerarchia dei tipi usati per creare l'interfaccia utente di un'applicazione Xamarin.Forms, che si trova nello spazio dei nomi `Xamarin.Forms`.

Questi tipi possono essere divisi in pagine, layout, visualizzazioni e celle. Una pagina Xamarin.Forms occupa in genere l'intera schermata e tutti i tipi di pagina derivano dalla classe [`Page`](xref:Xamarin.Forms.Page) . Le pagine contengono in genere un layout e tutti i tipi di layout derivano dalla classe [`Layout`](xref:Xamarin.Forms.Layout) . Un layout contiene in genere viste e possibilmente altri layout e tutti i tipi di visualizzazione derivano dalla classe [`View`](xref:Xamarin.Forms.View) . Infine, le celle sono controlli specializzati usati per la visualizzazione dei dati nei controlli [`TableView`](xref:Xamarin.Forms.TableView) e [`ListView`](xref:Xamarin.Forms.ListView) . Pagine, layout, visualizzazioni e celle sono tutti derivati dalla classe [`Element`](xref:Xamarin.Forms.Element) .

Il diagramma classi seguente mostra la gerarchia dei tipi che vengono in genere usati per compilare un'interfaccia utente in Xamarin.Forms:

[![Diagramma classi di controlli Xamarin.Forms](class-hierarchy-images/class-diagram.png "Diagramma classi di controlli Xamarin.Forms")](class-hierarchy-images/class-diagram-large.png#lightbox "Diagramma classi di controlli Xamarin.Forms")

> [!NOTE]
> È possibile scaricare una versione ad alta risoluzione del diagramma classi da [qui](class-hierarchy-images/class-diagram-high-resolution.png). Si noti tuttavia che il diagramma mostra solo un tipo di shell singolo.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento ai controlli Xamarin.Forms](~/xamarin-forms/user-interface/controls/index.md)
