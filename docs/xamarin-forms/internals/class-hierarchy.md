---
title: Gerarchia di classi di controlli Novell. Forms
description: Gli sviluppatori devono conoscere la gerarchia dei tipi usati per creare l'interfaccia utente di un'applicazione Novell. Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2019
ms.openlocfilehash: f08146d4439ff1fc22edea71ab1cbb337f64c037
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68984393"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Gerarchia di classi di controlli Novell. Forms

Novell. Forms è costituito da centinaia di tipi, in più spazi dei nomi. Gli sviluppatori dovrebbero avere familiarità con la gerarchia dei tipi utilizzati per creare l'interfaccia utente di un'applicazione Novell. Forms, che si trova `Xamarin.Forms` nello spazio dei nomi.

Questi tipi possono essere divisi in pagine, layout, visualizzazioni e celle. Una pagina Novell. Forms occupa in genere l'intera schermata e tutti i tipi di pagina derivano dalla [`Page`](xref:Xamarin.Forms.Page) classe. Le pagine contengono in genere un layout e tutti i tipi di layout derivano dalla [`Layout`](xref:Xamarin.Forms.Layout) classe. Un layout contiene in genere viste e possibilmente altri layout e tutti i tipi di vista derivano [`View`](xref:Xamarin.Forms.View) dalla classe. Infine, le celle sono controlli specializzati usati per la [`TableView`](xref:Xamarin.Forms.TableView) visualizzazione dei dati nei controlli e. [`ListView`](xref:Xamarin.Forms.ListView) Pagine, layout, visualizzazioni e celle sono tutti derivati dalla [`Element`](xref:Xamarin.Forms.Element) classe.

Il diagramma classi seguente mostra la gerarchia dei tipi che vengono in genere usati per compilare un'interfaccia utente in Novell. Forms:

[ ![Novell. Forms controlla il diagramma classi](class-hierarchy-images/class-diagram.png "Novell. Forms controlla il diagramma classi") ] (class-hierarchy-images/class-diagram-large.png#lightbox "Diagramma classi di controlli Novell. Forms")

> [!NOTE]
> È possibile scaricare una versione ad alta risoluzione del diagramma classi da [qui](class-hierarchy-images/class-diagram-high-resolution.png). Si noti tuttavia che il diagramma non mostra attualmente i `CarouselView` tipi e. `CollectionView` Questi verranno aggiunti quando i controlli non sono in anteprima. Inoltre, il diagramma mostra solo un singolo tipo di Shell.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento ai controlli Novell. Forms](~/xamarin-forms/user-interface/controls/index.md)
