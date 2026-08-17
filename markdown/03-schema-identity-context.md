# Schema, Identity, Context

In this chapter we describe extensions of the data graph – relating to schema, identity and context – that provide additional structures for accumulating knowledge. Henceforth, we refer to a *data graph* as a collection of data represented as nodes and edges using one of the models discussed in Chapter . We refer to a *knowledge graph* as a data graph potentially enhanced with representations of schema, identity, context, ontologies and/or rules. These additional representations may be embedded in the data graph, or layered above. Representations for schema, identity and context are discussed now, while ontologies and rules will be discussed in Chapter .

## Schema

One of the benefits of modelling data as graphs – versus, for example, the relational model – is the option to forgo or postpone the definition of a schema. However, when modelling data as graphs, schemata *can* be used to prescribe a high-level structure and/or semantics that the graph follows or should follow. We discuss three types of graph schemata: *semantic*, *validating*, and *emergent*.

### Semantic schema

A semantic schema allows for defining the meaning of high-level terms (aka *vocabulary* or *terminology*) used in the graph, which facilitates reasoning over graphs using those terms. Looking at Figure , for example, we may notice some natural groupings of nodes based on the types of entities to which they refer. We may thus decide to define *classes*, such as `Event`, `City`, etc., to denote these groupings. In fact, Figure  already illustrates three low-level classes – `Open Market`, `Food Market`, `Drinks Festival` – grouping similar entities with an edge labelled type. We may subsequently wish to capture some relations between some of these classes. In Figure , we present a class hierarchy for events where children are defined to be *sub-classes* of their parents such that if we find an edge  in our graph, we may also *infer* that  and  hold in the graph.

Aside from classes, we may also wish to define the semantics of edge labels, aka *properties*. Returning to Figure , we may consider that the properties city and venue are *sub-properties* of a more general property location, such that given an edge , for example, we may also infer that  must hold as an edge in the graph. We may also consider, for example, that bus and flight are both sub-properties of a more general property connects to. Along these lines, properties may also form a hierarchy similar to what we saw for classes. We may further define the *domain* of properties, indicating the class(es) of entities for nodes from which edges with that property extend; for example, we may define that the domain of connects to is a class `Place`, such that given the previous sub-property relations, we infer . Conversely, we may define the *range* of properties, indicating the class(es) of entities for nodes to which edges with that property extend; for example, we may define that the range of city is a class `City`, inferring that .

A prominent standard for defining a semantic schema for (RDF) graphs is the *RDF Schema* (*RDFS*) standard , which allows for defining sub-classes, sub-properties, domains, and ranges amongst the classes and properties used in an RDF graph, where such definitions can be serialised as a graph. We illustrate the semantics of these features in Table  and provide a concrete example of definitions in Figure  for a sample of terms used in the running example. These definitions can then be embedded into a data graph. More generally, the semantics of terms used in a graph can be defined in much more depth than seen here, as is supported by the *Web Ontology Language* (*OWL*) standard  for RDF graphs. We will return to such semantics later in Chapter .

[Table]

Semantic schemata are typically defined for incomplete graph data, where the absence of an edge between two nodes, such as , does not mean that the relation does not hold in the real world. Therefore, from the graph of Figure , we cannot assume that there is no flight between Viña del Mar and Arica. In contrast, if the *Closed World Assumption* (*CWA*) were adopted – as is the case in many classical database systems – it would be assumed that the data graph is a complete description of the world, thus allowing to assert with certainty that no flight exists between the two cities. Systems that do not adopt the CWA are said to adopt the *Open World Assumption* (*OWA*). Considering our running example, it would be unreasonable to assume that the tourism organisation has complete knowledge of everything describable in its knowledge graph, and hence adopting the OWA appears more appropriate. However, it can be inconvenient if a system is unable to definitely answer “*yes*” or “*no*” to questions such as “*is there a flight between Arica and Viña del Mar?*”, especially when the organisation is certain that it has complete knowledge of the flights. A compromise between OWA and CWA is the *Local Closed World Assumption* (*LCWA*), where portions of the data graph are assumed to be complete.

### Validating schema

When graphs are used to represent diverse, incomplete data at large scale, the OWA is the most appropriate choice for a *default* semantics. But in some scenarios, we may wish to guarantee that our data graph – or specific parts thereof – are in some sense “complete”. Returning to Figure , for example, we may wish to ensure that all events have at least a name, a venue, a start date, and an end date, such that applications using the data – e.g., one that sends event notifications to users – can ensure that they have the minimal information required. Furthermore, we may wish to ensure that the city of an event is *stated to be* a city (rather than *inferring* that it is a city). We can define such constraints in a validating schema and validate the data graph with respect to the resulting schema, listing constraint violations (if any). Thus while semantic schemata allow for inferring new graph data, validating schemata allow for validating a given data graph with respect to some constraints.

A standard way to define a validating schema for graphs is using *shapes* . A shape *targets* a set of nodes in a data graph and specifies *constraints* on those nodes. The shape’s target can be defined in many ways, such as targeting all instances of a class, the domain or range of a property, the result of a query, nodes connected to the target of another shape by a given property, etc. Constraints can then be defined on the targeted nodes, such as to restrict the number or types of values taken on a given property, the shapes that such values must satisfy, etc.

A *shapes graph* is formed from a set of interrelated shapes. Shapes graphs can be depicted as UML-like class diagrams, where Figure  illustrates an example of a shapes graph based on Figure , defining constraints on four interrelated shapes. Each shape – denoted with a box like Place, Event, etc. – is associated with a set of constraints. Nodes conform to a shape if and only if they satisfy all constraints defined on the shape. Inside each shape box are placed constraints on the number (e.g., `[1..*]` denotes one-to-many, `[1..1]` denotes precisely one, etc.) and types (e.g., `string`, `dateTime`, etc.) of nodes that conforming nodes can relate to with a property (e.g., name, start, etc.). Another option is to place constraints on the number of nodes conforming to a particular shape that the conforming node can relate to with a property (thus generating edges between shapes); for example,  denotes that conforming nodes for Event must relate to at least one node with the property venue that conforms to the Venue shape. Shapes can inherit the constraints of parent shapes – with inheritance denoted with an \(\triangle\) connector – as in the case of City and Venue, whose conforming nodes must also conform to the Place shape.

Given a shape and a targeted node, it is possible to check if the node conforms to that shape or not, which may require checking conformance of other nodes; for example, the node EID15 conforms to the Event shape not only based on its local properties, but also based on conformance of Santa Lucía to Venue and Santiago to City. Conformance dependencies may also be recursive, where the conformance of Santiago to City requires that it conforms to Place, which requires that Viña del Mar and Arica conform to Place, and so on. Conversely, EID16 does not conform to Event, as it does not have the start and end properties required by the example shapes graph.

When declaring shapes, the data modeller may not know in advance the entire set of properties that some nodes can have (now or in the future). An *open shape* allows the node to have additional properties not specified by the shape, while a *closed shape* does not. For example, if we add the edge  to the graph represented in Figure , then Santiago only conforms to the City shape if the shape is defined as open (since the shape does not mention founder).

Practical languages for shapes often support additional Boolean features, such as conjunction (*and*), disjunction (*or*), and negation (*not*) of shapes; for example, we may say that all the values of venue should conform to the shape Venue *and* (*not* City), making explicit that venues in the data graph should not be directly given as cities. However, shapes languages that freely combine recursion and negation may lead to semantic problems, depending on how their semantics are defined. To illustrate, consider the following case inspired by the barber paradox , involving a shape Barber whose conforming nodes shave at least one node conforming to Person *and* (*not* Barber). Now, given (only)  with Bob conforming to Person, does Bob conform to Barber? If *yes* – if Bob conforms to Barber – then Bob violates the constraint by not shaving at least one node conforming to Person *and* (*not* Barber). If *no* – if Bob does not conform to Barber – then Bob satisfies the Barber constraint by shaving such a node. Semantics to avoid such paradoxical situations have been proposed based on stratification , partial assignments , and stable models .

Although validating schemata and semantic schemata serve different purposes, they can complement each other. In particular, a validating schema can take into consideration a semantic schema, such that, for example, validation is applied on the data graph including inferences. Taking the class hierarchy of Figure  and the shapes graph of Figure , for example, we may define the target of the Event shape as the nodes that are of type `Event` (the class). If we first apply inferencing with respect to the class hierarchy of the semantic schema, the Event shape would now target EID15 and EID16. The presence of a semantic schema may, however, require adapting the validating schema. Taking into account, for example, the aforementioned class hierarchy would require defining a relaxed cardinality on the type property. Open shapes may also be preferred in such cases rather than enumerating constraints on all possible properties that may be inferred on a node.

Two shapes languages have recently emerged for RDF graphs: *Shape Expressions* (*ShEx*), published as a W3C Community Group Report ; and *SHACL* (*Shapes Constraint Language*), published as a W3C Recommendation . These languages support the discussed features (and more) and have been adopted for validating graphs in a number of domains relating to healthcare , scientific literature , spatial data , amongst others. More details about ShEx and SHACL can be found in the book by . A recently proposed language that can be used as a common basis for both ShEx and SHACL reveals their similarities and differences . A similar notion of schema has been proposed by  for property graphs.

We formally define shapes following the conventions of .

**Shape**

A *shape* \(\phi\) is defined as:

[Table]

where \(min \in \mathbb{N}_{(0)}\), \(max \in \mathbb{N}_{(0)} \cup \{ * \}\), with “\(*\)” indicating unbounded.

**Shapes schema**

A *shapes schema* is defined as a tuple \(\Sigma = (\Phi,S,\lambda)\) where \(\Phi\) is a set of shapes, \(S\) is a set of shape labels, and \(\lambda : S \rightarrow \Phi\) is a total function from labels to shapes.

The shapes schema from Figure  can be expressed as:

[Table]

For example, Event is a shape label (an element of \(S\)) that maps to a shape (an element of \(\phi\)). This mapping is defined by \(\lambda\).

In a shapes schema, shapes may refer to other shapes, giving rise to a graph that is sometimes known as the *shapes graph* . Figure  illustrates a shapes graph of this form.

The semantics of a shape is defined in terms of the evaluation of that shape over each node of a given data graph. The semantics of a shapes schema, in turn, is the result of evaluating each shape of the schema over each node of a given data graph; the result of this evaluation is a *shapes map*.

**Shapes map**

Given a directed edge-labelled graph \(G = (V,E,L)\) and a shapes schema \(\Sigma = (\Phi,S,\lambda)\), a *shapes map* is a (partial) mapping \(\sigma: V \times S \rightarrow \{ 0, 1 \}\).

The shapes map \(\sigma\) is a way of labelling the nodes of \(G\) with the labels of shapes from \(S\). If \(\sigma(v,s) = 1\), then node \(v\) is labelled \(s\) (possibly amongst other labels); otherwise if \(\sigma(v,s) = 0\), then node \(v\) is not labelled \(s\). The precise semantics depends on  whether or not \(\sigma\) is a total or partial mapping: whether or not it is defined for every pair in \(V \times S\). Herein we present the semantics for the more straightforward case wherein \(\sigma\) is assumed to be a total shapes map.

**Shape evaluation**

Given a shapes schema \(\Sigma \coloneqq (\Phi,S,\lambda)\), a directed edge-labelled graph \(G = (V,E,L)\), a node \(v \in V\) and a total shapes map \(\sigma\), the *shape evaluation function* \(\semantics{\phi}{G}{v}{\sigma} \in \{ 0 , 1 \}\) is defined as follows:

[Table]

If \(\semantics{\phi}{G}{v}{\sigma} = 1\), then \(v\) is said to *satisfy* \(\phi\) in \(G\) under \(\sigma\).

Typically for the purposes of validating a graph with respect to a shapes schema, a *target* is defined that requires certain nodes to satisfy certain shapes.

**Shapes target**

Given a directed edge-labelled graph \(G = (V,E,L)\) and a shapes schema \(\Sigma = (\Phi,S,\lambda)\), a *shapes target* \(T \subseteq V \times S\) is a set of pairs of nodes and shape labels from \(G\) and \(\Sigma\), respectively.

The nodes that a shape targets can be selected a manual selection, based on the type(s) of the nodes, based on the results of a graph query, etc. .

Lastly, we define the notion of a valid graph under a given shapes schema and target based on the existence of a shapes map satisfying certain conditions.

**Valid graph**

Given a shapes schema \(\Sigma = (\Phi,S,\lambda)\), a directed edge-labelled graph \(G = (V,E,L)\), and a shapes target \(T\), we say that *\(G\) is valid under \(\Sigma\) and \(T\)* if and only if there exists a shapes map \(\sigma\) such that, for all \(s \in S\) and \(v \in V\) it holds that \(\sigma(v,s) = \semantics{\lambda(s)}{G}{v}{\sigma}\), and \((v,s) \in T\) implies \(\sigma(v,s) = 1\).

Taking the graph \(G\) from Figure  and the shapes schema \(\Sigma\) from Figure , first assume an empty shapes target \(T = \{\}\). If we consider a shapes map where (e.g.) \(\sigma(\)EID15, Event\() = 1\), \(\sigma(\)Santa Lucía, Venue\() = 1\), \(\sigma(\)Santa Lucía, Place\() = 1\), etc., but where \(\sigma(\)EID16, Event\() = 0\) (as it does not have the required values for start and end), etc., then we see that \(G\) is valid under \(\Sigma\) and \(T\). However, if we were to define a shapes target \(T\) to ensure that the Event shape targets EID15 and EID16 – i.e., to define \(T\) such that \(\{ (\)EID15, Event\(), (\)EID16, Event\() \} \subseteq T\) – then the graph would no longer be valid under \(\Sigma\) and \(T\) since EID16 does not satisfy Event.

The semantics we present here assumes that each node in the graph either satisfies or does not satisfy each shape labelled by the schema. More complex semantics – for example, based on Kleene’s three-valued logic  – have been proposed that support partial shapes maps, where the satisfaction of some nodes for some shapes can be left as undefined. Shapes languages in practice may support other more advanced forms of constraints, such as counting on paths . In terms of implementing validation with respect to shapes, work has been done on translating constraints into sets of graph queries, whose results are input to a SAT solver for recursive cases .

### Emergent schema

Both semantic and validating schemata require a domain expert to explicitly specify definitions and constraints. However, a data graph will often exhibit latent structures that can be automatically extracted as an *emergent schema*  (aka *graph summary* ).

A framework often used for defining emergent schema is that of *quotient graphs*, which partition groups of nodes in the data graph according to some equivalence relation while preserving some structural properties of the graph. Taking Figure , we can intuitively distinguish different *types* of nodes based on their context, such as event nodes, which link to venue nodes, which in turn link to city nodes, and so forth. In order to describe the structure of the graph, we could consider six partitions of nodes: *event*, *name*, *venue*, *class*, *date-time*, *city*. In practice, these partitions may be computed based on the class or shape of the node. Merging the nodes of each partition into one node while preserving edges leads to the quotient graph shown in Figure : the nodes of this quotient graph are the partitions of nodes from the data graph and an edge  is included in the quotient graph if and only if there exists \(x \in X\) and \(z \in Z\) such that  is in the original data graph.

There are many ways in which quotient graphs may be defined, depending not only on how nodes are partitioned, but also how the edges are defined. Different quotient graphs may provide different guarantees with respect to the structure they preserve. Formally, we can say that every quotient graph *simulates* its input graph (based on the *simulation relation* of set membership between data nodes and quotient nodes), meaning that for all \(x \in X\) with \(x\) an input node and \(X\) a quotient node, if  is an edge in the data graph, then there must exist an edge  in the quotient graph such that \(z \in Z\); for example, the quotient graph of Figure  simulates the data graph of Figure . However, this quotient graph seems to suggest (for instance) that EID16 would have a start and end date in the data graph when this is not the case. A stronger notion of structural preservation is given by *bisimilarity*, which in this case would further require that if  is an edge in the quotient graph, then for all \(x \in X\), there must exist a \(z \in Z\) such that  is in the data graph; this is not satisfied by EID16 in the quotient graph of Figure , which does not have an outgoing edge labelled start or end in the original data graph. Figure  illustrates a bisimilar version of the quotient graph, splitting the *event* partition into two nodes reflecting their different outgoing edges. An interesting property of bisimilarity is that it preserves forward-directed paths: given a path expression \(r\) without inverses and two bisimilar graphs, \(r\) will match a path in one graph if and only if it matches a corresponding path in the other bisimilar graph. One can verify, for example, that a path matches  in Figure  if and only if there is a path matching  in Figure  such that \(x \in X\) and \(z \in Z\).

There are many ways in which quotient graphs may be defined, depending on the equivalence relation that partitions nodes. Furthermore, there are many ways in which other similar or bisimilar graphs can be defined, depending on the (bi)simulation relation that preserves the data graph’s structure . Such techniques aim to *summarise* the data graph into a higher-level topology. In order to reduce the memory overhead of the quotient graph, in practice, nodes may rather be labelled with the cardinality of the partition and/or a high-level label (e.g., *event*, *city*) for the partition rather than storing the labels of all nodes in the partition.

Various other forms of emergent schema not directly based on a quotient graph framework have also been proposed; examples include emergent schemata based on relational tables , and baseed on formal concept analysis . Emergent schemata may be used to provide a human-understandable overview of the data graph, to aid with the definition of a semantic or validating schema, to optimise the indexing and querying of the graph, to guide the integration of data graphs, and so forth. We refer to the survey by  dedicated to the topic for further details.

Emergent schemata are often based on the notion of a quotient graph.

**Quotient graph**

Given a directed edge-labelled graph \(G = (V,E,L)\), a graph \(\mathcal{G} = (\mathcal{V},\mathcal{E},L)\) is a *quotient graph* of \(G\) if and only if:

- \(\mathcal{V}\) is a partition of \(V\) without the empty set, i.e., \(\mathcal{V} \subseteq (2^V - \emptyset)\), \(V = \bigcup_{U\in \mathcal{V}} U\), and for all \(U\in \mathcal{V}\), \(W\in \mathcal{V}\), it holds that \(U = W\) or \(U \cap W = \emptyset\); *and*
- \(\mathcal{E} = \{ (U,l,W) \mid U \in \mathcal{V}, W \in \mathcal{V} \text{ and } \exists u \in U, \exists w \in W : (u,l,w) \in E \} \).

A quotient graph can “merge” multiple nodes into one node, keeping the edges of its constituent nodes. For an input graph \(G = (V,E,L)\), there is an exponential number of possible quotient graphs based on partitions of the input nodes. On one extreme, the input graph is a quotient graph of itself (turning nodes like u into singleton nodes like \(\{\)u\(\}\)). On the other extreme, a single node \(V\), with all input nodes, and loops \((V,l,V)\) for each edge-label \(l\) used in the set of input edges \(E\), is also a quotient graph. Quotient graphs typically fall somewhere in between, where the partition \(\mathcal{V}\) of \(V\) is often defined in terms of an *equivalence relation* \(\sim\) on the set \(V\) such that \(\mathcal{V} \coloneqq {\sim}/V\); i.e., \(\mathcal{V}\) is defined as the *quotient set* of \(V\) with respect to \(\sim\); for example, we might define an equivalence relation on nodes such that \(u \sim v\) if and only if they have the same set of defined types, where \({\sim}/V\) is then a partition whose parts contain all nodes with the same types. Another way to induce a quotient graph is to define the partition in a way that preserves some of the topology (i.e., connectivity) of the input graph. One way to formally define this idea is through *simulation* and *bisimulation*.

**Simulation**

Given two directed edge-labelled graph \(G = (V,E,L)\) and \(G' = (V',E',L')\), let \(R \subseteq V \times V'\) be a relation between the nodes of \(G\) and \(G'\), respectively. We call \(R\) a *simulation* on \(G\) and \(G'\) if, for all \((v,v') \in R\), the following holds:

- if \((v,p,w) \in E\) then there exists \(w'\) such that \((v',p,w') \in E'\) and \((w,w') \in R\).

If a simulation exists on \(G\) and \(G'\), we say that \(G'\) *simulates* \(G\), denoted \(G \rightsquigarrow G'\).

**Bisimulation**

If \(R\) is a simulation on \(G\) and \(G'\), we call it a *bisimulation* if, for all \((v,v') \in R\), the following condition holds:

- if \((v'p,w') \in E'\) then there exists \(w\) such that \((v,p,w) \in E\) and \((w,w') \in R\).

If a bisimulation exists on \(G\) and \(G'\), we call them *bisimilar*, denoted \(G \approx G'\).

Bisimulation (\(\approx\)) is then an equivalence relation on graphs. By defining the (bi)simulation relation \(R\) in terms of set membership \(\in\), every quotient graph simulates its input graph, but does not necessarily bisimulate its input graph. This gives rise to the notion of *bisimilar quotient graphs*.

Figures  and  exemplify quotient graphs for the graph of Figure . Figure  simulates but is not bisimilar to the data graph. Figure  is bisimilar to the data graph. Often the goal will be to compute the most concise quotient graph that satisfies a given condition; for example, the nodes without outgoing edges in Figure  could be merged while preserving bisimilarity.

## Identity

Figure  uses nodes like Santiago, but to which Santiago does this node refer? Do we refer to Santiago de Chile, Santiago de Cuba, Santiago de Compostela, or do we perhaps refer to the indie rock band Santiago? Based on edges such as , we may deduce that it is one of the three cities mentioned (not the rock band), and based on the fact that the graph describes tourist attractions in Chile, we may further deduce that it refers to Santiago de Chile. Without further details, however, *disambiguating* nodes of this form may rely on heuristics prone to error in more difficult cases. To help avoid such ambiguity, first we may use globally-unique identifiers to avoid naming clashes when the knowledge graph is extended with external data, and second we may add external identity links to disambiguate a node with respect to an external source.

### Persistent identifiers

Assume we wished to compare tourism in Chile and Cuba, and we have acquired an appropriate knowledge graph for Cuba similar to the one we have for Chile. We can merge two graphs by taking their union. However, as shown in Figure , using an ambiguous node like Santiago may yield a *naming clash*: the node is referring to two different real-world cities in both graphs, where the merged graph indicates that Santiago is a city in both Chile and Cuba (rather than two distinct cities). To avoid such clashes, long-lasting *persistent identifiers* (*PIDs*)  can be created in order to uniquely identify an entity; examples of PID schemes include *Digital Object Identifiers* (*DOIs*) for papers, *ORCID iDs* for authors, *International Standard Book Numbers* (*ISBNs*) for books, *Alpha-2 codes* for counties, and more besides.

In the context of the Semantic Web, the RDF data model goes one step further and recommends that global Web identifiers be used for nodes and edge labels. However, rather than adopt the *Uniform Resource Locators (URLs)* used to identify the location of *information resources* such as webpages, RDF 1.1 proposes to use *Internationalised Resource Identifiers (IRIs)* to identify *non-information resources* such as cities or events. Hence, for example, in the RDF representation of the Wikidata  – a knowledge graph proposed to complement Wikipedia, discussed in more detail in Chapter  – while the URL https://www.wikidata.org/wiki/Q2887 refers to a webpage that can be loaded in a browser providing human-readable metadata about Santiago, the IRI http://www.wikidata.org/entity/Q2887 refers to the city itself. Distinguishing the identifiers for the webpage and the city itself avoids naming clashes; for example, if we use the URL to identify both the webpage and the city, we may end up with an edge in our graph, such as (with readable labels below the edge):

`[Santiago (URL)]``[founded by (URL)]` `[Pedro de Valdivia (URL)]`

Such an edge leaves ambiguity: was Pedro de Valdivia the founder of the webpage, or the city? Using IRIs for entities distinct from the URLs for the webpages that describe them avoids such ambiguous cases, where Wikidata thus rather defines the previous edge using less ambiguous identifiers, as follows:

`[Santiago (IRI)]``[founded by (IRI)]` `[Pedro de Valdivia (IRI)]`

using IRIs for the city, person, and founder of, distinct from the webpages describing them. These Wikidata identifiers use the prefix http://www.wikidata.org/entity/ for entities and the prefix http://www.wikidata.org/prop/direct/ for relations. Such prefixes are known as *namespaces*, and are often abbreviated with prefix strings, such as `wd:` or `wdt:`, where the latter edge can then be written more concisely using such abbreviations as th edge .

If HTTP IRIs are used to identify the graph’s entities, when the IRI is looked up (via HTTP), the web-server can return (or redirect to) a description of that entity in formats such as RDF. This further enables RDF graphs to link to related entities described in external RDF graphs over the Web, giving rise to *Linked Data*  (discussed in Chapter ). Though HTTP IRIs offer a flexible and powerful mechanism for issuing global identifiers on the Web, they are not necessarily persistent: websites may go offline, the resources described at a given location may change, etc. In order to enhance the persistence of such identifiers, *Persistent URL* (*PURL*) services offer redirects from a central server to a particular location, where the PURL can be redirected to a new location if necessary, changing the address of a document without changing its identifier. The persistence of HTTP IRIs can then be improved by using namespaces defined through PURL services.

### External identity links

Assume that the tourist board opts to define the `chile:` namespace with an IRI such as `http://turismo.cl/entity/` on a web-server that they control, allowing nodes such as chile:Santiago – a shortcut for the IRI http://turismo.cl/entity/Santiago – to be looked up over the Web. While using such a naming scheme helps to avoid naming clashes, the use of IRIs does not necessarily help ground the identity of a resource. For example, an external geographic knowledge graph may assign the same city the IRI geo:SantiagoDeChile in their own namespace, where we have no direct way of knowing that the two identifiers refer to the same city. If we merge the two knowledge graphs, we will end up with two distinct nodes for the same city, and thus not integrate their data.

There are a number of ways to ground the identity of an entity. The first is to associate the entity with uniquely-identifying information in the graph, such as its geo-coordinates, its postal code, the year it was founded, etc. Each additional piece of information removes ambiguity regarding which city is being referred to, providing (for example) more options for matching the city with its analogue in external sources. A second option is to use *identity links* to state that a local entity has the same identity as another *coreferent* entity found in an external source; an instantiation of this concept can be found in the OWL standard, which defines the `owl:sameAs` property relating coreferent entities. Using this property, we could state the edge  in our RDF graph, thus establishing an identity link between the corresponding nodes in both graphs. Rather than specifying pairwise identity links between all knowledge graphs, it suffices if two knowledge graphs provide corresponding identity links to the same external knowledge graph, such as DBpedia or Wikidata; for example, if the local knowledge graph provides an identity link to Wikidata indicating , while the remote knowledge graph has the identity link , then we can infer . The semantics of `owl:sameAs` defined by the OWL standard then allows us to combine the data for both nodes. Such semantics will be discussed later in Chapter . Ways in which identity links can be computed will also be discussed later in Chapter .

### Datatypes

Consider the two date-times on the left of Figure : how should we assign these nodes persistent/global identifiers? Intuitively it would not make much sense, for example, to assign IRIs to these nodes since their syntactic form tells us what they refer to: specific dates and times in March 2020. This syntactic form is further recognisable by machine, meaning that with appropriate software, we could order such values in ascending or descending order, extract the year, etc.

Most practical data models for graphs allow for defining nodes that are datatype values. RDF utilises *XML Schema Datatypes* (*XSD*) , amongst others, where a datatype node is given as a pair \((l,d)\) where \(l\) is a lexical string, such as “`2020-03-29T20:00:00`”, and \(d\) is an IRI denoting the datatype, such as `xsd:dateTime`. The node is then denoted `"2020-03-29T20:00:00"^^xsd:dateTime`. Datatype nodes in RDF are called *literals* and are not allowed to have outgoing edges. Other datatypes commonly used in RDF data include `xsd:string`, `xsd:integer`, `xsd:decimal`, `xsd:boolean`, etc. If the datatype is omitted, the value is assumed to be of type `xsd:string`. Applications built on top of RDF can then recognise these datatypes, parse them into datatype objects, and apply equality checks, normalisation, ordering, transformations, etc., according to their standard definition. In the context of property graphs, Neo4j  also defines a set of internal datatypes on property values that includes numbers, strings, Booleans, spatial points, and temporal values.

### Lexicalisation

Global identifiers for entities will sometimes have a human-interpretable form, such as chile:Santiago, but the identifier strings themselves do not carry any formal semantic significance. In other cases, the identifiers used may not be human-interpretable by design. In Wikidata, for instance, Santiago de Chile is identified as wd:Q2887, where such a scheme has the advantage of providing better persistence and of not being biased to a particular human language. As a real-world example, the Wikidata identifier for Eswatini (wd:Q1050) was not affected when the country changed its name from Swaziland, and does not necessitate choosing between languages for creating (more readable) IRIs such as wd:Eswatini (English), wd:eSwatini (Swazi), wd:Esuatini (Spanish), etc.

Since identifiers can be arbitrary, it is common to add edges that provide a human-interpretable label for nodes, such as , indicating how people may refer to the subject node linguistically. Linguistic information of this form plays an important role in grounding knowledge such that users can more clearly identify which real-world entity a particular node in a knowledge graph actually references ; it further permits cross-referencing entity labels with text corpora to find, for example, documents that potentially speak of a given entity . Labels can be complemented with aliases (e.g., ) or comments (e.g. ) to further help ground the node’s identity.

Nodes such as `"Santiago"` denote string literals, rather than an identifier. Depending on the specific graph model, such literal nodes may also be defined as a pair \((s,l)\), where \(s\) denotes the string and \(l\) a language code; in RDF, for example we may state , , etc., indicating labels for the node in different languages. In other models, the pertinent language can rather be specified, e.g., via metadata on the edge. Knowledge graphs with human-interpretable labels, aliases, comments, etc., (in various languages) are sometimes called (*multilingual*) *lexicalised knowledge graphs* ".

### Existential nodes

When modelling incomplete information, we may in some cases know that there must exist a particular node in the graph with particular relationships to other nodes, but without being able to identify the node in question. For example, we may have two co-located events chile:EID42 and chile:EID43 whose venue has yet to be announced. One option is to simply omit the venue edges, in which case we lose the information that these events have a venue and that both events have the same venue. Another option might be to create a fresh IRI representing the venue, but semantically this becomes indistinguishable from there being a known venue. Hence some graph models permit the use of existential nodes, represented here as a blank circle:

chile:EID42chile:venue     chile:venuechile:EID43

These edges denote that there exists a common venue for chile:EID42 and chile:EID42 without identifying it. Existential nodes are supported in RDF as blank nodes , which are also commonly used to support modelling complex elements in graphs, such as *RDF lists* . Figure  exemplifies an RDF list, which uses blank nodes in a linked-list structure to encode order. Though existential nodes can be convenient, their presence can complicate operations on graphs, such as deciding if two data graphs have the same structure modulo existential nodes . Hence methods for *skolemising* existential nodes in graphs – replacing them with canonical labels – have been proposed . Other authors rather call to minimise the use of such nodes in graph data .

## Context

Many (arguably *all*) facts presented in the data graph of Figure  can be considered true with respect to a certain *context*. With respect to *temporal context*, Santiago has existed as a city since 1541, flights from Arica to Santiago began in 1956, etc. With respect to *geographic context*, the graph describes events in Chile. With respect to *provenance*, data relating to EID15 were taken from – and are thus said to be true with respect to – the Ñam webpage on January 4th, 2020. Other forms of context may also be used. We may further combine contexts, such as to indicate that Arica is a Chilean city (*geographic*) since 1883 (*temporal*) per the Treaty of Ancón (*provenance*).

By context we herein refer to the *scope of truth*, i.e., the context in which some data are held to be true . The graph of Figure  leaves much of its context implicit. However, making context explicit can allow for interpreting the data from different perspectives, such as to understand what held true in 2016, what holds true excluding webpages later found to have spurious data, etc. As seen previously, context for graph data may be considered at different levels: on individual nodes, individual edges, or sets of edges (sub-graphs). We now discuss various representations by which context can be made explicit at different levels.

### Direct representation

The first way to represent context is to consider it as data no different from other data. For example, the dates for the event EID15 in Figure  can be seen as representing a form of temporal context, indicating the temporal scope within which edges such as  are held true. Another option is to change a relation represented as an edge, such as , into a node, such as seen in Figure , allowing us to assign additional context to the relation. While in these examples context is represented in an ad hoc manner, a number of specifications have been proposed to represent context as data in a more standard way. One example is the *Time Ontology* , which specifies how temporal entities, intervals, time instants, etc. – and relations between them such as *before*, *overlaps*, etc. – can be described in RDF graphs in an interoperable manner. Another example is the *PROV Data Model* , which specifies how provenance can be described in RDF graphs, where entities (e.g., graphs, nodes, physical document) are derived from other entities, are generated and/or used by activities (e.g., extraction, authorship), and are attributed to agents (e.g., people, software, organisations).

### Reification

Often we may wish to directly define the context of edges themselves; for example, we may wish to state that the edge  is valid from 1956. While we could use the pattern of turning the edge into a node – as illustrated in Figure  – to directly represent such context, another option is to use *reification*, which allows for making statements about statements in a generic manner (or in the case of a graph, for defining edges about edges). In Figure  we present three forms of reification that can be used for modelling temporal context on the aforementioned edge within a directed edge-labelled graph . We use \(e\) to denote an arbitrary identifier representing the edge itself to which the context can be associated. Unlike in a direct representation, \(e\) represents an edge, not a flight. RDF reification  (Figure ) defines a new node \(e\) to represent the edge and connects it to the source node (via subject), target node (via object), and edge label (via predicate) of the edge. In contrast, \(n\)-ary relations  (Figure ) connect the source node of the edge directly to the edge node \(e\) with the label of the edge; the target node of the edge is then connected to \(e\) (via value). Finally, singleton properties  (Figure ) rather use \(e\) as an edge label, connecting it to a node indicating the original edge label (via singleton). Other forms of reification have been proposed in the literature, including, for example, NdFluents . In general, a reified edge does not assert the edge it reifies; for example, we may reify an edge to state that it is no longer valid. We refer to  for further comparison of reification alternatives.

Three representations of temporal context on a directed labelled edge

### Higher-arity representation

As an alternative to reification, we can rather use higher-arity representations for modelling context. Taking again the edge , Figure  illustrates three higher-arity representations of temporal context. First, we can use a named graph (Figure ) to contain the edge and then define the temporal context on the graph name. Second, we can use a property graph (Figure ) where the temporal context is defined as a property on the edge. Third, we can use *RDF**  (Figure ): an extension of RDF that allows edges to be defined as nodes. Amongst these options, the most flexible is the named graph representation, where we can assign context to multiple edges at once by placing them in one named graph; for example, we can add more edges to the named graph of Figure  that are also valid from 1956. The least flexible option is RDF*, which, in the absence of an edge id, does not permit different groups of contextual values to be assigned to an edge; for example, if we add four contextual values to the edge , to state that it was valid from 2006 until 2010 and valid from 2014 until 2018, we cannot pair the values, but may rather have to create a node to represent different presidencies (in the other models, we could have used two named graphs or edge ids).

Three higher-arity representations of temporal context on an edge

### Annotations

Thus far, we have discussed representing context in a graph, but we have not spoken about automated mechanisms for reasoning about context; for example, if there are only seasonal summer flights from Santiago to Arica, we may wish to find other routes from Santiago for winter events taking place in Arica. While the dates for buses, flights, etc., can be represented directly in the graph, or using reification, writing a query to manually intersect the corresponding temporal contexts will be difficult. An alternative is to consider *annotations* that provide mathematical definitions of a contextual domain and key operations over that domain that can be applied automatically.

Some annotations model a particular contextual domain; for example, *Temporal RDF*  allows for annotating edges with time intervals, such as , while *Fuzzy RDF*  allows for annotating edges with a degree of truth such as , indicating that it is more-or-less true – with a degree of \(0.8\) – that Santiago has a semi-arid climate.

Other forms of annotation are domain-independent; for example, *Annotated RDF*  allows for representing context modelled as *semi-rings*: algebraic structures consisting of domain values (e.g., temporal intervals, fuzzy values, etc.) and two operators to combine domain values: *meet* and *join*. We provide an example in Figure , where \(G\) is annotated with values from a temporal domain using sets of integers (\(1{-}365\) to represent days of the year. For brevity we use intervals, where, e.g., \(\{[150,152]\}\) denotes the set \(\{150,151,152\}\). Query \(Q\) then asks for flights from Santiago to cities with events; this query will check and return an annotation reflecting the temporal validity of each answer. To derive these answers, we require a conjunction of annotations on compatible flight and city edges, using the *meet operator* to compute the annotation for which both edges hold. The natural way to define meet here is as the intersection of sets of days, where, for example, applying meet on the event annotation \(\color{blue}\{[150,152]\}\) and the flight annotation \(\color{blue}\{[1,120],[220,365]\}\) for Punta Arenas leads to the empty time interval \(\color{blue}\{\}\), which may thus lead to the city being filtered from the results (depending on the query evaluation semantics). However, for Arica, we find two different non-empty intersections: \(\color{blue}\{[123,125]\}\) for EID16 and \(\color{blue}\{[276,279]\}\) for EID17. Given that we are interested in just the city (a projected variable), we can combine the two annotations for Arica using the *join operator*, returning the annotation in which either result holds true. The natural way to define join is as the union of the sets of days, giving \(\color{blue}\{[123,125],[276,279]\}\).

We define an annotation domain per .

**Annotation domain**

Let \(A\) be a set of *annotation values*. An *annotation domain* is an idempotent, commutative semi-ring \(D = \langle A,\oplus,\otimes,\bot,\top \rangle\).

This definition can then instantiate specific domains of context.

Letting \(D\) be a semi-ring imposes that, for any values \(a, a_1, a_2, a_3\) in \(A\), the following hold:

- \((a_1 \oplus a_2) \oplus a_3 = a_1 \oplus (a_2 \oplus a_3)\)
- \((\bot \oplus a) = (a \oplus \bot) = a\)
- \((a_1 \oplus a_2) = (a_2 \oplus a_1)\)
- \((a_1 \otimes a_2) = (a_2 \otimes a_1)\)
- \((a_1 \otimes a_2) \otimes a_3 = a_1 \otimes (a_2 \otimes a_3)\)
- \((\top \otimes a) = (a \otimes \top) = a\)
- \(a_1 \otimes (a_2 \oplus a_3) = (a_1 \otimes a_2) \oplus (a_1 \otimes a_3)\)
- \((a_1 \oplus a_2) \otimes a_3 = (a_1 \otimes a_3) \oplus (a_2 \otimes a_3)\)
- \((\bot \otimes a) = (a \otimes \bot) = \bot\)

The requirement that it be idempotent further imposes the following:

- \((a \oplus a) = a\)

Finally, the requirement that it be commutative imposes the following:

- \((a_1 \otimes a_2) = (a_2 \otimes a_1)\)

Idempotence induces a partial order: \(a_1 \leq a_2\) if and only if \(a_1 \oplus a_2 = a_2\). Imposing these conditions on the annotation domain allow for reasoning and querying to be conducted over the annotation domain in a well-defined manner. Annotated graphs can then be defined in the natural way:

**Annotated directed edge-labelled graph**

Letting \(D = \langle A,\oplus,\otimes,\bot,\top \rangle\) denote an idempotent, commutative semi-ring, we define an *annotated directed edge-labelled graph* (or *annotated directed edge-labelled graph*) as \(G = (V,E_A,L)\) where \(V \subseteq \con\) is a set of nodes, \(L \subseteq \con\) is a set of edge labels, and \(E_A \subseteq V \times L \times V \times A\) is a set of edges annotated with values from \(A\).

Figure  exemplifies query answering on a graph annotated with days of the year. Formally this domain can be defined as follows: \(A \coloneqq 2^{\mathbb{N}_{[1,365]}}\), \(\oplus \coloneqq \cup\), \(\otimes \coloneqq \cap\), \(\top \coloneqq \mathbb{N}_{[1,365]}\), \(\bot \coloneqq \emptyset\), where one may verify that \(D = \langle 2^{\mathbb{N}_{[1,365]}}, \cup, \cap, \mathbb{N}_{[1,365]}, \emptyset \rangle\) is indeed an idempotent, commutative semi-ring.

### Other contextual frameworks

Other frameworks have been proposed for modelling and reasoning about context in graphs. A notable example is that of *contextual knowledge repositories* , which allow for assigning individual (sub-)graphs to their own context. Unlike in the case of named graphs, context is explicitly modelled along one or more dimensions, where each (sub-)graph takes a value for each dimension. Each dimension is associated with a partial order over its values – e.g., 2020-03-22 \(\preceq\) 2020-03 \(\preceq\) 2020 – enabling the selection and combination of sub-graphs that are valid within contexts at different granularities.  similarly propose a form of contextual OnLine Analytic Processing (OLAP), based on a data cube formed by dimensions where each cell contains a knowledge graph. Operations such as “*slice-and-dice*” (selecting knowledge according to given dimensions), as well as “*roll-up*” (aggregating knowledge at a higher level) are supported. We refer the reader to the respective papers for more details .
