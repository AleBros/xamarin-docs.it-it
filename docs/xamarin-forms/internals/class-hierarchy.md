---
title: Xamarin.FormsGerarchia di classi di controlli
description: Gli sviluppatori devono avere familiarità con la gerarchia dei tipi utilizzati per creare l'interfaccia utente di un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0087e2bb81c7c9204a782519a9eeb9891adc297a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138639"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin.FormsGerarchia di classi di controlli

Xamarin.Formsè costituito da centinaia di tipi, in più spazi dei nomi. Gli sviluppatori dovrebbero avere familiarità con la gerarchia dei tipi utilizzati per creare l'interfaccia utente di un' Xamarin.Forms applicazione, che si trova nello `Xamarin.Forms` spazio dei nomi.

Questi tipi possono essere divisi in pagine, layout, visualizzazioni e celle. Una Xamarin.Forms pagina in genere occupa l'intera schermata e tutti i tipi di pagina derivano dalla [`Page`](xref:Xamarin.Forms.Page) classe. Le pagine contengono in genere un layout e tutti i tipi di layout derivano dalla [`Layout`](xref:Xamarin.Forms.Layout) classe. Un layout contiene in genere viste e possibilmente altri layout e tutti i tipi di visualizzazione derivano dalla [`View`](xref:Xamarin.Forms.View) classe. Infine, le celle sono controlli specializzati usati per la visualizzazione dei dati nei [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) controlli e. Pagine, layout, visualizzazioni e celle sono tutti derivati dalla [`Element`](xref:Xamarin.Forms.Element) classe.

Il diagramma classi seguente mostra la gerarchia dei tipi che vengono in genere usati per compilare un'interfaccia utente in Xamarin.Forms :

[![Xamarin.FormsDiagramma classi controlli](class-hierarchy-images/class-diagram.png "[! OP. Il diagramma delle classi di controlli NO-LOC (Novell. Forms)]")](class-hierarchy-images/class-diagram-large.png#lightbox "[! OP. Il diagramma delle classi di controlli NO-LOC (Novell. Forms)]")

> [!NOTE]
> È possibile scaricare una versione ad alta risoluzione del diagramma classi da [qui](class-hierarchy-images/class-diagram-high-resolution.png). Si noti tuttavia che il diagramma mostra solo un tipo di shell singolo.

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsRiferimento ai controlli](~/xamarin-forms/user-interface/controls/index.md)
