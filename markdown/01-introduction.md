# Introduction

Though the phrase “knowledge graph” has been used in the literature since at least 1972 , the modern incarnation of the phrase stems from the 2012 announcement of the Google Knowledge Graph , followed by further announcements of knowledge graphs by Airbnb , Amazon , eBay , Facebook , IBM , LinkedIn , Microsoft , Uber , and more besides. The growing industrial uptake of the concept proved difficult for academia to ignore: more and more scientific literature is being published on knowledge graphs, which includes books (e.g., ), as well as papers outlining definitions (e.g., ), novel techniques (e.g., ), and surveys of specific aspects of knowledge graphs (e.g., ).

Underlying all such developments is the core idea of using graphs to represent data, often enhanced with some way to explicitly represent knowledge . The result is most often used in application scenarios that involve integrating, managing and extracting value from diverse sources of data at large scale . Employing a graph-based abstraction of knowledge has numerous benefits in such settings when compared with, for example, a relational model or NoSQL alternatives. Graphs provide a concise and intuitive abstraction for a variety of domains, where edges capture the (potentially cyclical) relations between the entities inherent in social data, biological interactions, bibliographical citations and co-authorships, transport networks, and so forth . Graphs allow maintainers to postpone the definition of a schema, allowing the data – and its scope – to evolve in a more flexible manner than typically possible in a relational setting, particularly for capturing incomplete knowledge . Unlike (other) NoSQL models, specialised graph query languages support not only standard relational operators (joins, unions, projections, etc.), but also navigational operators for recursively finding entities connected through arbitrary-length paths . Standard knowledge representation formalisms – such as ontologies  and rules  – can be employed to define and reason about the semantics of the terms used to label and describe the nodes and edges in the graph. Scalable frameworks for graph analytics  can be leveraged for computing centrality, clustering, summarisation, etc., in order to gain insights about the domain being described. Various representations have also been developed that support applying machine learning techniques both directly and indirectly over graphs .

In summary, the decision to build and use a knowledge graph opens up a range of techniques that can be brought to bear for integrating and extracting value from diverse sources of data at large scale. The goal of this book is to motivate and give a comprehensive introduction to knowledge graphs: to describe their foundational data models and how they can be queried; to discuss representations relating to schema, identity, and context; to discuss deductive and inductive ways to make knowledge explicit; to present a variety of techniques that can be used for the creation and enrichment of graph-structured data; to describe how the quality of knowledge graphs can be discerned and how they can be refined; to discuss standards and best practices by which knowledge graphs can be published; and to provide an overview of existing knowledge graphs found in practice. Our intended audience includes researchers and practitioners who are new to knowledge graphs. As such, we do not assume that readers have specific expertise on knowledge graphs.

*Knowledge graph*. The definition of a “*knowledge graph*” remains contentious , where a number of (sometimes conflicting) definitions have emerged, varying from specific technical proposals to more inclusive general proposals; we address these prior definitions in Appendix . Herein we adopt an inclusive definition, where we view a knowledge graph as *a graph of data intended to accumulate and convey knowledge of the real world, whose nodes represent entities of interest and whose edges represent relations between these entities*. The graph of data (aka *data graph*) conforms to a graph-based data model, which may be a *directed edge-labelled graph*, a *property graph*, etc. (we discuss concrete alternatives in Chapter ). By *knowledge*, we refer to something that is *known*. Such knowledge may be accumulated from external sources, or extracted from the knowledge graph itself. Knowledge may be composed of simple statements, such as “*Santiago is the capital of Chile*”, or quantified statements, such as “*all capitals are cities*”. Simple statements can be accumulated as edges in the data graph. If the knowledge graph intends to accumulate quantified statements, a more expressive way to represent knowledge – such as *ontologies* or *rules* – is required. *Deductive methods* can then be used to entail and accumulate further knowledge (e.g., “*Santiago is a city*”). Additional knowledge – based on simple or quantified statements – can also be extracted from and accumulated by the knowledge graph using *inductive methods*.

Knowledge graphs are often assembled from numerous sources, and as a result, can be highly diverse in terms of structure and granularity. To address this diversity, representations of *schema*, *identity*, and *context* often play a key role, where a *schema* defines a high-level structure for the knowledge graph, *identity* denotes which nodes in the graph (or in external sources) refer to the same real-world entity, while *context* may indicate a specific setting in which some unit of knowledge is held true. As aforementioned, effective methods for *extraction*, *enrichment*, *quality assessment*, and *refinement* are required for a knowledge graph to grow and improve over time.

*In practice*. Knowledge graphs aim to serve as an ever-evolving shared substrate of knowledge within an organisation or community . We distinguish two types of knowledge graphs in practice: *open knowledge graphs* and *enterprise knowledge graphs*. Open knowledge graphs are published online, making their content accessible for the public good. The most prominent examples – DBpedia , Freebase , Wikidata , YAGO , etc. – cover many domains and are either extracted from Wikipedia , or built by communities of volunteers . Open knowledge graphs have also been published within specific domains, such as media , government , geography , tourism , life sciences , and more besides. Enterprise knowledge graphs are typically internal to a company and applied for commercial use-cases . Prominent industries using enterprise knowledge graphs include Web search (e.g., Bing , Google ), commerce (e.g., Airbnb , Amazon , eBay , Uber ), social networks (e.g., Facebook , LinkedIn ), finance (e.g., Accenture , Banca d’Italia , Bloomberg , Capital One , Wells Fargo ), among others. Applications include search , recommendations , personal agents , advertising , business analytics , risk assessment , automation , and more besides. We will provide more details on the use of knowledge graphs in practice in Chapter .

*Running example*. To keep the discussion accessible, throughout the book, we present concrete examples in the context of a hypothetical knowledge graph relating to tourism in Chile (loosely inspired by related use-cases ). The knowledge graph is managed by a tourism board that aims to increase tourism in the country and promote new attractions in strategic areas. The knowledge graph itself will eventually describe tourist attractions, cultural events, services, businesses, travel routes, etc. Some applications the organisation envisages are to:

- create a tourism portal that allows visitors to search for attractions, upcoming events, and other related services (in multiple languages);
- gain insights into tourism demographics in terms of season, nationalities, etc.;
- analyse sentiment about tourist attractions, including positive reviews, summaries of complaints about events and services, crime reports, etc.;
- understand tourism trajectories: the sequence of attractions, events, etc., that tourists often visit;
- cross-reference these tourism trajectories with currently available flights, buses, etc., to suggest new strategic routes for public transport;
- offer personalised recommendations of places to visit;
- and so forth.

*Outline*. The remainder of the book is structured as follows:

**Chapter **

outlines graph data models and the languages used to query them.

**Chapter **

describes representations of schema, identity, and context for graphs.

**Chapter **

presents deductive formalisms for representing and entailing knowledge.

**Chapter **

describes inductive techniques for learning from graphs.

**Chapter **

discusses the creation and enrichment of knowledge graphs.

**Chapter **

enumerates dimensions for assessing knowledge graph quality.

**Chapter **

discusses various techniques for knowledge graph refinement.

**Chapter **

introduces principles and protocols for publishing knowledge graphs.

**Chapter **

surveys some prominent knowledge graphs and their applications.

**Chapter **

concludes with future directions for knowledge graphs.

**Appendix **

outlines the historical background for knowledge graphs.
