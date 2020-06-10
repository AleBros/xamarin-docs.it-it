---
title: " Xamarin.Forms Controlla gerarchia di classi" Descrizione: "gli sviluppatori devono conoscere la gerarchia dei tipi usati per creare l'interfaccia utente di un' Xamarin.Forms applicazione."
ms. prod: Novell MS. AssetID: C89E6B98-464D-4BBE-BF11-13A5FCBBF420 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 01/07/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
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
