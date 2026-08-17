# Data Graphs

At the foundation of any knowledge graph is the principle of first applying a graph abstraction to data, resulting in an initial data graph. We now discuss a selection of graph-structured data models that are commonly used in practice to represent data graphs. We then discuss the primitives that form the basis of graph query languages used to interrogate such data graphs.

## Models

Leaving aside graphs, let us assume that the tourism board from our running example has not yet decided how to model relevant data about attractions, events, services, etc. The board first considers using a tabular structure – in particular, relational databases – to represent the required data, and though they do not know precisely what data they will need to capture, they begin to design an initial relational schema. They begin with an Event table with five columns:

Event(name, venue, type, start, end)

where name and start together form the primary key of the table in order to uniquely identify recurring events. But as they start to populate the data, they encounter various issues: events may have multiple names (e.g., in different languages), events may have multiple venues, they may not yet know the start and end date-times for future events, events may have multiple types, and so forth. Incrementally addressing these modelling issues as the data become more diverse, they generate internal identifiers for events and adapt their relational schema until they have:

EventName(id,name), EventStart(id,start), EventEnd(id,end), EventVenue(id,venue), EventType(id,type)(2.1)

With the above schema, the organisation can now model events with \(0{-}n\) names, venues, and types, and \(0{-}1\) start dates and end dates (without needing relational nulls).

Along the way, the board has to incrementally change the schema several times in order to support new sources of data. Each such change requires a costly remodelling, reloading, and reindexing of data; here we only considered one table. The tourism board struggles with the relational model because they do not know, *a priori*, what data will need to be modelled or what sources they will use. But once they reach the latter relational schema, the board finds that they can integrate further sources without more changes: with minimal assumptions on *multiplicities* (\(1{-}1\), \(1{-}n\), etc.) this schema offers a lot of flexibility for integrating incomplete and diverse data.

In fact, the refined, flexible schema that the board ends up with – as shown in () – is modelling a set of binary relations between entities, which indeed can be viewed as modelling a graph. By instead adopting a graph data model from the outset, the board could forgo the need for an upfront schema, and could define any (binary) relation between any pair of entities at any time.

We now introduce graph data models popular in practice .

### Directed edge-labelled graphs

A directed edge-labelled graph (sometimes known as a *multi-relational graph* ) is defined as a set of nodes – like Santiago, Arica, EID16, 2018-03-22 12:00 – and a set of directed labelled edges between those nodes, like . In the case of knowledge graphs, nodes are used to represent entities and edges are used to represent (binary) relations between those entities. Figure  provides an example of how the tourism board could model some relevant event data as a directed edge-labelled graph. The graph includes data about the names, types, start and end date-times, and venues for events. Adding information to such a graph typically involves adding new nodes and edges (with some exceptions discussed later). Representing incomplete information requires simply omitting a particular edge; for example, the graph does not yet define a start/end date-time for the Food Truck festival.

Modelling data as a graph in this way offers more flexibility for integrating new sources of data, compared to the standard relational model, where a schema must be defined upfront and followed at each step. While other structured data models such as trees (XML, JSON, etc.) would offer similar flexibility, graphs do not require organising the data hierarchically (should `venue` be a parent, child, or sibling of `type` for example?). They also allow cycles to be represented and queried (e.g., note the directed cycle in the routes between Santiago, Arica, and Viña del Mar).

A standardised data model based on directed edge-labelled graphs is the Resource Description Framework (RDF) , which has been recommended by the W3C. The RDF model defines different types of nodes, including *Internationalized Resource Identifiers* (IRIs)  which allow for global identification of entities on the Web; *literals*, which allow for representing strings (with or without language tags) and other datatype values (integers, dates, etc.); and *blank nodes*, which are anonymous nodes that are not assigned an identifier (for example, rather than create internal identifiers like `EID15`, `EID16`, in RDF, we have the option to use blank nodes). We will discuss these different types of nodes further in Section  when we speak about issues relating to identity.

We now formally define a directed edge-labelled graph, where we denote by \(\con\) a countably infinite set of constants.

**Directed edge-labelled graph**

A *directed edge-labelled graph* is a tuple \(G = (V,E,L)\), where \(V \subseteq \con\) is a set of nodes, \(L \subseteq \con\) is a set of edge labels, and \(E \subseteq V \times L \times V\) is a set of edges.

In reference to Figure , the set of nodes \(V\) has 15 elements, including `Arica`, `EID16`, etc. The set of edges \(E\) has 23 triples, including (`Arica`, `flight`, `Santiago`). Bidirectional edges are represented with two edges. The set of edge labels \(L\) has 8 elements, including `start`, `flight`, etc.

Definition  does not state that \(V\) and \(L\) are disjoint: though not present in the example, a node can also serve as an edge-label. The definition also permits that nodes and edge labels can be present without any associated edge. Either restriction could be explicitly stated – if necessary – in a particular application while still conforming to a directed edge-labelled graph.

For ease of presentation, we may treat a set of (directed labelled) edges \(E \subseteq V \times L \times V\) as a directed edge-labelled graph \((V,E,L)\), in which case we refer to the graph induced by \(E\) assuming that \(V\) and \(L\) contain all and only those nodes and edge labels, respectively, used in \(E\). We may similarly apply set operators on directed edge-labelled graphs, which should be interpreted as applying to their sets of edges; for example, given \(G_1 = (V_1,E_1,L_1)\) and \(G_2 = (V_2,E_2,L_2)\), by \(G_1 \cup G_2\) we refer to the directed edge-labelled graph induced by \(E_1 \cup E_2\).

### Heterogeneous graphs

A heterogeneous graph  (or *heterogeneous information network* ) is a directed graph where each node and edge is assigned one type. Heterogeneous graphs are thus akin to directed edge-labelled graphs – with edge labels corresponding to edge types – but where the type of node forms part of the graph model itself, rather than being expressed with a relation (as seen in Figure ). An edge is called *homogeneous* if it is between two nodes of the same type (e.g., borders in Figure ); otherwise it is called *heterogeneous* (e.g., capital in Figure ). Heterogeneous graphs allow for partitioning nodes according to their type, for example, for the purposes of machine learning tasks . Conversely, such graphs typically only support a many-to-one relation between nodes and types, which is not the case for directed edge-labelled graphs (see, for example, the node Santiago with zero types and EID15 with multiple types in Figure ).

Comparing directed edge-labelled graphs and heterogeneous graphs

We next define the notion of a heterogeneous graph.

**Heterogeneous graph**

A *heterogeneous graph* is a tuple \(G = (V,E,L,l)\), where \(V \subseteq \con\) is a set of nodes, \(L \subseteq \con\) is a set of edge/node labels, \(E \subseteq V \times L \times V\) is a set of edges, and \(l : V \rightarrow L\) maps each node to a label.

In reference to Figure , the set of nodes \(V\) has three elements: `Santiago`, `Chile`, and `Perú`. The set of edges \(E\) has 3 triples, including (`Santiago`, `capital`, `Chile`). The set of edge labels \(L\) has 4 elements: `capital`, `borders`, `City`, `Country`. Finally, with respect to the node labels, \(l(\)`Santiago`\() =\) `City`, \(l(\)`Chile`\() =\) `Country`, and \(l(\)`Perú`\() =\) `Country`.

In heterogeneous graphs, edge and node labels are often called *types*. By rather defining edges with labels as per directed edge-labelled graphs – rather than separately labelling edges with \(l\) – two nodes can be related by \(n\) edges with \(n\) different labels; for example, we can represent both \((\)`Santiago`, `capital`, `Chile`\()\) and \((\)`Santiago`, `country`, `Chile`\()\) as edges in the heterogeneous graph.

### Property graphs

Property graphs constitute an alternative graph model that offers additional flexibility when modelling more complex relations. Consider integrating incoming data that provide further details on which companies offer fares on which flights, allowing the board to better understand available routes between cities (for example, on national airlines). In the case of directed edge-labelled graphs, we cannot directly annotate an edge like  with the company (or companies) offering that route. But we could add a new node denoting a flight, connect it with the source, destination, companies, and mode, as shown in Figure . Applying this modelling to all routes in Figure  would, however, involve significant changes.

The property graph model was thus proposed to offer additional flexibility when modelling data as a graph . A property graph allows a set of *property–value* pairs and a *label* to be associated with both nodes and edges. Figure  depicts an example of a property graph with data analogous to Figure . We use property–value pairs on edges to model the companies. The type of relation is captured by the label `flight`. We further use node labels to indicate the types of the two nodes, and property–value pairs for their latitude and longitude.

Comparing directed edge-labelled graphs and property graphs

Property graphs are prominently used in graph databases, such as Neo4j . Property graphs can be converted to/from directed edge-labelled graphs  (per, e.g., Figure ). In summary, directed edge-labelled graphs offer a more minimal model, while property graphs offer a more flexible one. Often the choice of model will be secondary to other practical factors, such as the implementations available for different models, etc.

We formally define a property graph.

**Property graph**

A *property graph* is a tuple \(G = (V,E,L,P,U,e,l,p)\), where \(V \subseteq \con\) is a set of node ids, \(E \subseteq \con\) is a set of edge ids, \(L \subseteq \con\) is a set of labels, \(P \subseteq \con\) is a set of properties, \(U \subseteq \con\) is a set of values, \(e : E \rightarrow V \times V\) maps an edge id to a pair of node ids, \(l : V \cup E \rightarrow 2^L\) maps a node or edge id to a set of labels, and \(p : V \cup E \rightarrow 2^{P \times U}\) maps a node or edge id to a set of property–value pairs.

Returning to Figure :

- the set \(V\) contains `Santiago` and `Arica`;
- the set \(E\) contains `LA380` and `LA381`;
- the set \(L\) contains `Capital City`, `Port City`, and `flight`;
- the set \(P\) contains `lat`, `long`, and `company`;
- the set \(U\) contains `–33.45`, `–70.66`, `LATAM`, `–18.48`, and `–70.33`;
- the mapping \(e\) gives, for example, \(e(\)`LA380`\() = (\)`Santiago`, `Arica`\()\);
- the mapping \(l\) gives, for example, \(l(\)`Santiago`\() =\{ \)`Capital City`\(\}\) and \(l(\)`LA380`\() =\)\(\{ \)`flight`\(\}\);
- the mapping \(p\) gives, for example, \(p(\)`LA380`\() =\{ (\)`company`, `LATAM`\() \}\) and \(p(\)`Santiago`\() =\)\(\{ (\)`lat`, `–33.45`\(), (\)`long`, `–70.66`\() \}\).

Unlike previous definitions , we allow a node or edge to have several values for a given property. In practice, systems like Neo4j  may rather support this by allowing a single array (i.e., list) of values.

### Graph dataset

Although multiple directed edge-labelled graphs can be merged by taking their union, it is often desirable to manage several graphs rather than one monolithic graph; for example, it may be beneficial to manage multiple graphs from different sources, making it possible to update or refine data from one source, to distinguish untrustworthy sources from more trustworthy ones, and so forth. A graph dataset then consists of a set of *named graphs* and a *default graph*. Each named graph is a pair of a graph ID and a graph. The default graph is a graph without an ID, and is referenced “by default” if a graph ID is not specified. Figure  provides an example where events and routes are stored in two named graphs, and the default graph manages metadata about the named graphs. Graph names can also be used as nodes in a graph. Furthermore, nodes and edges can be repeated across graphs, where the same node in different graphs will typically refer to the same entity, allowing data on that entity to be integrated when merging graphs. Though the example depicts a dataset of directed edge-labelled graphs, the concept generalises straightforwardly to datasets of other types of graphs.

An RDF dataset is a graph dataset model standardised by the W3C  where each graph is an RDF graph, and graph names can be blank nodes or IRIs. A prominent use-case for RDF datasets is to manage and query *Linked Data* composed of interlinked documents of RDF graphs spanning the Web. When dealing with Web data, tracking the source of data becomes of key importance . We will discuss Linked Data later in Section  and further discuss provenance in Section .

We more formally define a graph dataset. We assume that all data graphs featured in a given graph dataset follow the same model (directed edge-labelled graph, heterogeneous graph, property graph, etc).

**Graph dataset**

A *named graph* is a pair \((n,G)\) where \(G\) is a data graph, and \(n \in \con\) is a graph name. A *graph dataset* is a pair \(D = (G_D,N)\) where \(G_D\) is a data graph called the *default graph* and \(N\) is either the empty set, or a set of named graphs \(\{ (n_1,G_1), \ldots (n_k,G_k) \}\) (\(k > 0\)) such that if \(i \neq j\) then  \(n_i \neq n_j\) (for all \(1 \leq i \leq k\), \(1 \leq j \leq k\)).

Figure  provides an example of a directed edge-labelled graph dataset \(D\) consisting of two named graphs and a default graph. The default graph does not have a name associated with it. The two graph names are `Events` and `Routes`; these are also used as nodes in the default graph.

### Other graph data models

The previous models are popular examples of graph representations. Other graph data models exist with *complex nodes* that may contain individual edges  or nested graphs  (sometimes called *hypernodes* ). Likewise the mathematical notion of a *hypergraph* defines *complex edges* that connect sets rather than pairs of nodes. In our view, a knowledge graph can adopt any such graph data model based on nodes and edges: often data can be converted from one model to another (see Figure  vs. Figure ). In the rest of the book, we prefer discussing directed edge-labelled graphs given their relative succinctness, but most discussion extends naturally to other models.

### Graph stores

A variety of techniques have been proposed for storing and indexing graphs, facilitating the efficient evaluation of queries (as discussed next). Directed edge-labelled graphs can be stored in relational databases either as a single relation of arity three (*triple table*), as a binary relation for each property (*vertical partitioning*), or as \(n\)-ary relations for entities of a given type (*property tables*) . Custom (so-called *native*) storage techniques have also been developed for a variety of graph models, providing efficient access for finding nodes, edges and their adjacent elements . A number of systems further allow for distributing graphs over multiple machines based on popular NoSQL stores or custom partitioning schemes . For further details we refer to the book chapter by  and the survey by  dedicated to this topic.

## Querying

A number of languages have been proposed for querying graphs , including the SPARQL query language for RDF graphs ; and Cypher , Gremlin , and G-CORE  for querying property graphs. We refer to  for an investigation of the popularity of these languages. Underlying these query languages are some common primitives, including (basic) graph patterns, relational operators, path expressions, and more besides . We now describe these core features for querying graphs in turn, starting with basic graph patterns.

### Basic graph patterns

At the core of every structured query language for graphs lie *basic graph patterns* , which follow the same model as the data graph being queried (see Section ), additionally allowing variables as terms. Terms in basic graph patterns are thus divided into constants, such as Arica or venue, and variables, which we prefix with question marks, such as ?event or ?rel. A basic graph pattern is then evaluated against the data graph by generating mappings from the variables of the graph pattern to constants in the data graph such that the image of the graph pattern under the mapping (replacing variables with the assigned constants) is contained within the data graph.

Figure  provides an example of a basic graph pattern looking for the venues of Food Festivals, along with the possible mappings generated by the graph pattern against the data graph of Figure . In some of the presented mappings (the last two listed), multiple variables are mapped to the same term, which may or may not be desirable depending on the application. Hence a number of semantics have been proposed for evaluating basic graph patterns , amongst which the most important are: *homomorphism-based semantics*, which allows multiple variables to be mapped to the same term such that all mappings shown in Figure  would be considered results; and *isomorphism-based semantics*, which requires variables on nodes and/or edges to be mapped to unique terms, thus excluding the latter three mappings of Figure  from the results. Different languages may adopt different semantics for evaluating basic graph patterns; for example, SPARQL adopts a homomorphism-based semantics, while Cypher adopts an isomorphism-based semantics specifically on edges (while allowing multiple variables to map to one node).

As we will see in later examples (particularly Figure ), basic graph patterns may also form cycles (be they directed or undirected), and may replace edge labels with variables. Basic graph patterns in the context of other models – such as property graphs – can be defined analogously by allowing variables to replace constants in any position of the model.

We formalise basic graph patterns first for directed edge-labelled graphs, and subsequently for property graphs . For these definitions, we introduce a countably infinite set of *variables* \(\var\) ranging over (but disjoint from: \(\con \cap \var = \emptyset\)) the set of constants. We refer generically to constants and variables as *terms*, denoted and defined as \(\term = \con \cup \var\). We define a basic graph pattern for a particular graph data model by simply replacing constants with terms (that may be variables). Though we focus on directed edge-labelled graphs and property graphs, basic graph patterns for other graph models can be defined analogously.

**Basic directed edge-labelled graph pattern**

We define a *basic directed edge-labelled graph pattern* as a tuple \(Q = (V,E,L)\), where \(V \subseteq \term\) is a set of node terms, \(L \subseteq \term\) is a set of edge terms, and \(E \subseteq V \times L \times V\) is a set of edges (triple patterns).

Returning to the example of Figure :

- the set \(V\) contains the constant `Food Festival` and variables `?ev`, `?vn1` and `?vn2`;
- the set \(E\) contains four edges, including \((\)`?ev`, `type`, `Food Festival`\()\);
- the set \(L\) contains the constants `type` and `venue`.

A basic property graph pattern is also defined by introducing variables.

**Basic property graph pattern**

We define a *basic property graph pattern* as a tuple \(Q = (V,E,L,P,U,e,l,p)\), where \(V \subseteq \term\) is a set of node id terms, \(E \subseteq \term\) is a set of edge id terms, \(L \subseteq \term\) is a set of label terms, \(P \subseteq \term\) is a set of property terms, \(U \subseteq \term\) is a set of value terms, \(e : E \rightarrow V \times V\) maps an edge id term to a pair of node id terms, \(l : V \cup E \rightarrow 2^{L}\) maps a node or edge id term to a set of label terms, and \(p : V \cup E \rightarrow 2^{P \times U}\) maps a node or edge id term to a set of pairs of property–value terms.

Towards defining the results of evaluating a basic graph pattern over a data graph (following the same model), we first define a partial mapping \(\mu : \var \rightarrow \con\) from variables to constants, whose *domain* (the set of variables for which it is defined) is denoted by \(\dom(\mu)\). Given a basic graph pattern \(Q\), let \(\var(Q)\) denote the set of all variables appearing in (some recursively nested element of) \(Q\). We further denote by \(\mu(Q)\) the image of \(Q\) under \(\mu\), meaning that any variable \(v \in \var(Q) \cap \dom(\mu)\) is replaced in \(Q\) by \(\mu(v)\). Observe that when \(\var(Q) \subseteq \dom(\mu)\), then \(\mu(Q)\) is a data graph (in the corresponding model of \(Q\)).

Next, we define the notion of containment between data graphs. For two directed edge-labelled graphs \(G_1 = (V_1,E_1,L_1)\) and \(G_2 = (V_2,E_2,L_2)\), we say that \(G_1\) is a *sub-graph* of \(G_2\), denoted \(G_1 \subseteq G_2\), if and only if \(V_1 \subseteq V_2\), \(E_1 \subseteq E_2\), and \(L_1 \subseteq L_2\). For two property graphs \(G_1 = (V_1,E_1,L_1,P_1,U_1,e_1,l_1,p_1)\) and \(G_2 = (V_2,E_2,L_2,P_2,U_2,e_2,l_2,p_2)\), we say that \(G_1\) is a *sub-graph* of \(G_2\), denoted \(G_1 \subseteq G_2\), if and only if \(V_1 \subseteq V_2\), \(E_1 \subseteq E_2\), \(L_1 \subseteq L_2\), \(P_1 \subseteq P_2\), \(U_1 \subseteq U_2\), for all \(x \in E_1\) it holds that \(e_1(x) = e_2(x)\), and for all \(y \in E_1 \cup V_1\) it holds that \(l_1(y) \subseteq l_2(y)\) and \(p_1(y) \subseteq p_2(y)\).

We are now ready to define the evaluation of a basic graph pattern.

**Evaluation of a basic graph pattern**

Let \(Q\) be a basic graph pattern and let \(G\) be a data graph (in the same model). We then define the *evaluation of the basic graph pattern \(Q\) over the data graph \(G\)*, denoted \(Q(G)\), to be the set of mappings \(Q(G) = \{ \mu \mid \mu(Q) \subseteq G \text{ and } \dom(\mu) = \var(Q) \}\).

Figure  enumerates all of the mappings given by the evaluation of the depicted basic graph pattern over the data graph of Figure . Each non-header row indicates a mapping \(\mu\).

The final results of evaluating a basic graph pattern may vary depending on the choice of semantics: the results under *homomorphism-based semantics* are defined as \(Q(G)\). Conversely, under *isomorphism-based* semantics, mappings that send two edge variables to the same constant and/or mappings that send two node variables to the same constant may be excluded from the results. Henceforth we assume the more general *homomorphism-based semantics*.

### Complex graph patterns

A (basic) graph pattern transforms an input graph into a table of results (as shown in Figure ). We may then consider using the relational algebra to combine and/or transform such tables, thus forming more complex queries from one or more graph patterns. Recall that the relational algebra consists of unary operators that accept one input table, and binary operators that accept two input tables. Unary operators include projection (\(\pi\)) to output a subset of columns, selection (\(\sigma\)) to output a subset of rows matching a given condition, and renaming of columns (\(\rho\)). Binary operators include union (\(\cup\)) to merge the rows of two tables into one table, difference (\(-\)) to remove the rows from the first table present in the second table, and joins (\(\Join\)) to extend the rows of one table with rows from the other table that satisfy a join condition. Selection and join conditions typically include equalities (\(=\)), inequalities (\(\leq\)), negation (\(\neg\)), disjunction (\(\vee\)), etc. From these operators, we can further define other (syntactic) operators, such as intersection (\(\cap\)) to output rows in both tables, anti-join (\(\rhd\), aka *minus*) to output rows from the first table for which there are no join-compatible rows in the second table, left-join (, aka *optional*) to perform a join but keeping rows from the first table without a compatible row in the second table, etc.

Basic graph patterns can then be expressed in a subset of relational algebra (namely \(\pi\), \(\sigma\), \(\rho\), \(\Join\)). Assuming, for example, a single ternary relation \(G(s,p,o)\) representing a graph – i.e., a table \(G\) with three columns \(s\), \(p\), \(o\) – the query of Figure  can be expressed in relational algebra as:

\(\pi_{ev,vn_1,vn_2}(\sigma_{p=\texttt{type} \wedge o=\texttt{Food Festival} \wedge p_1=p_2=\texttt{venue}}(\rho_{s/ev}(G \bowtie \rho_{p/p_1,o/vn_1}(G) \bowtie \rho_{p/p_2,o/vn_2}(G))))\)

where \(\Join\) denotes a *natural join*, meaning that equality is checked across pairs of columns with the same name in both tables (here, the join is thus performed on the subject column \(s\)). The result of this query is a table with a column for each variable: \(ev,vn1,vn2\). However, not all queries using \(\pi, \sigma, \rho\) and \(\Join\) on \(G\) can be expressed as basic graph patterns; for example, we cannot choose which variables to project in a basic graph pattern, but rather must project all variables not fixed to a constant.

Graph query languages such as SPARQL  and Cypher  allow the full use of relational operators over the results of graph patterns, giving rise to *complex graph patterns* . Figure  presents an example of a complex graph pattern with projected variables in bold, choosing particular variables to appear in the final results. In Figure , we give another example of a complex graph pattern looking for food festivals or drinks festivals not held in Santiago, optionally returning their start date and name (where available).

Complex graph patterns can give rise to duplicate results; for example, the first result in Figure  appears twice since `?city1` matches `Arica` and `?city2` matches `Viña del Mar` in one result, and vice-versa in the other. Query languages then offer two semantics: *bag semantics* preserves duplicates according to the multiplicity of the underlying mappings, while *set semantics* (typically invoked with a `DISTINCT` keyword) removes duplicates from the results.

We now formally define complex graph patterns.

**Complex graph pattern**

*Complex graph patterns* are defined recursively, as follows:

- If \(Q\) is a basic graph pattern, then \(Q\) is a *complex graph pattern*.
- If \(Q\) is a complex graph pattern, and \(\mathcal{V} \subseteq \var(Q)\), then \(\pi_\mathcal{V}(Q)\) is a *complex graph pattern*.
- If \(Q\) is a complex graph pattern, and \(R\) is a selection condition with Boolean and equality connectives (\(\wedge\), \(\vee\), \(\neg\), \(=\)), then \(\sigma_R(Q)\) is a *complex graph pattern*.
- If both \(Q_1\) and \(Q_2\) are complex graph patterns, then \(Q_1 \Join Q_2\), \(Q_1 \cup Q_2\), \(Q_1 - Q_2\) and \(Q_1 \rhd Q_2\) are also *complex graph patterns*.

We now define the evaluation of complex graph patterns. Given a mapping \(\mu\), for a set of variables \(\mathcal{V} \subseteq \var\) let \(\mu[\mathcal{V}]\) denote the mapping \(\mu'\) such that \(\dom(\mu') = \dom(\mu) \cap \mathcal{V}\) and \(\mu'(v) = \mu(v)\) for all \(v \in \dom(\mu')\) (in other words, \(\mu[\mathcal{V}]\) projects the variables \(\mathcal{V}\) from \(\mu\)). Letting \(R\) denote a Boolean selection condition and \(\mu\) a mapping, we denote by \(\mu \models R\) that \(\mu\) satisfies the Boolean condition. Finally, we define two mappings \(\mu_1\) and \(\mu_2\) to be *compatible*, denoted \(\mu_1 \sim \mu_2\), if and only if \(\mu_1(v) = \mu_2(v)\) for all \(v \in \dom(\mu_1) \cap \dom(\mu_2)\) (i.e., they map common variables to the same constant). We are now ready to provide the definition.

**Complex graph pattern evaluation**

Given a complex graph pattern \(Q\), if \(Q\) is a basic graph pattern, then \(Q(G)\) is defined per Definition . Otherwise, \(Q(G)\) is defined as follows:
\begin{align*}
\pi_\mathcal{V}(Q)(G) = & \,\{ \mu[\mathcal{V}] \mid \mu \in Q(G) \} \\
\sigma_R(Q)(G) = & \, \{ \mu \mid \mu \in Q(G)\text{ and }\mu \models R\}\\
Q_1 \Join Q_2(G) = & \,\{ \mu_1 \cup \mu_2 \mid \mu_1 \in Q_2(G), \mu_2 \in Q_1(G)\text{ and }\mu_1 \sim \mu_2 \} \\
Q_1 \cup Q_2(G) = & \,\{ \mu \mid \mu \in Q_1(G)\text{ or } \mu \in Q_2(G) \} \\
Q_1 - Q_2(G) = & \,\{ \mu \mid \mu \in Q_1(G)\text{ and } \mu \notin Q_2(G) \} \\
Q_1 \rhd Q_2(G) = & \,\{ \mu \mid \mu \in Q_1(G)\text{ and }\nexists \mu_2 \in Q_2(G)\text{ such that }\mu \sim \mu_2 \}
\end{align*}

Based on these operators, we can define some additional syntactic operators, such as the *left-join* (, aka *optional*):

\begin{align*}
Q_1  Q_2(G) = & \,(Q_1(G) \Join Q_2(G)) \cup (Q_1(G) \rhd Q_2(G))
\end{align*}

We call such operators *syntactic* as they do not add expressivity.

Figure  illustrates a complex graph pattern and its evaluation.

### Navigational graph patterns

A key feature that distinguishes graph query languages is the ability to include *path expressions* in queries. A path expression \(r\) is a regular expression that allows for matching arbitrary-length paths between two nodes using a *regular path query* \((x,r,y)\), where \(x\) and \(y\) can be variables or constants (or even the same term). The base path expression is where \(r\) is a constant (an edge label). Furthermore if \(r\) is a path expression, then \(r^*\) (*Kleene star*: zero-or-more) is also a path expression. Finally, if \(r_1\) and \(r_2\) are path expressions, then \(r_1 \mid r_2\) (*disjunction*) and \(r_1 \cdot r_2\) (*concatenation*) are also path expressions. A related notion is that of *2-way regular path queries*, which also allow for querying inverse paths; specifically, if \(r\) is path expression, then it is a *2-way path expression*, and if \(r\) is a *2-way path expression*, then \(r^-\) (*inverse*) is a *2-way path expression*. Henceforth we will refer generically to both the 1-way and 2-way variants as path expressions and regular path queries.

Regular path queries can be evaluated under a number of different semantics. For example, \((\)`Arica`, `bus*`, `?city`\()\) evaluated against the graph of Figure  may match the paths shown in Figure . In fact, since a cycle is present, an infinite number of paths are potentially matched. For this reason, restricted semantics are often applied, returning only the shortest paths, or paths without repeated nodes or edges (as in the case of Cypher). Rather than returning paths, another option is to instead return the (finite) set of pairs of nodes connected by a matching path (as in the case of SPARQL 1.1).

Regular path queries can then be used in basic graph patterns to express *navigational graph patterns* , as shown in Figure , which illustrates a query searching for food festivals in cities reachable (recursively) from Arica by bus or flight. Furthermore, when regular path queries and graph patterns are combined with operators such as projection, selection, union, difference, and optional, the result is known as *complex navigational graph patterns* .

We first define path expressions and regular path queries.

**Path expression**

A constant (edge label) \(c\) is a *path expression*. Furthermore, if \(r\), \(r_1\) and \(r_2\) are path expressions, then:

- \(r^-\) (*inverse*) and \(r^*\) (*Kleene star*) are *path expressions*.
- \(r_1 \cdot r_2\) (*concatenation*) and \(r_1 \mid r_2\) (*disjunction*) are *path expressions*.

We now define the evaluation of a path expression on a directed-edge labelled graph under the SPARQL 1.1-style semantics whereby the endpoints (pairs of start and end nodes) of the path are returned .

**Path evaluation (directed edge-labelled graph)**

Given a directed edge-labelled graph \(G = (V,E,L)\) and a path expression \(r\), we define the *evaluation of \(r\) over \(G\)*, denoted \(r[G]\), as follows:
\begin{align*}
r[G] = &\, \{ (u,v) \mid (u,r,v) \in E \} \,(\text{for }r \in \con) \\
r^-[G] = &\, \{ (u,v) \mid (v,u) \in r[G] \} \\
r_1 \mid r_2[G] = &\, r_1[G] \cup r_2[G] \\
r_1 \cdot r_2[G] = &\, \{ (u,v) \mid \exists w \in V : (u,w) \in r_1[G]\text{ and }(w,v) \in r_2[G]\}\\
r^*[G] = &\, \{ (u,u) \mid u \in V \} \cup \bigcup_{n \in \mathbb{N^+}} r^n[G]
\end{align*}
where by \(r^n\) we denote the \(n\)th-concatenation of \(r\) (e.g., \(r^3 = r \cdot r \cdot r\)).

The inclusion of the reflexive pairs \((u,u)\) in the definition of \(r^*[G]\) captures zero-length paths. For example, in the query \((\)`Arica`, `bus*`, `?city`\()\), the reflexive pair \((\)`Arica`, `Arica`\()\) ensures that the variable `?city` will also match `Arica` via the zero-length path.

The evaluation of a path expression on a property graph \(G = (V,E,L,P,U,e,l,p)\) can be defined analogously by adapting the first definition (in the case that \(r \in \con\)) as follows:

\[ r[G] = \{(u,v) \mid \exists x \in E : e(x) = (u,v)\text{ and }l(e) = r \} \,.\]

The rest of the definitions then remain unchanged.

Query languages may support additional operators, some of which are syntactic (e.g., \(r^+\) is sometimes used for one-or-more, but can be rewritten as \(r \cdot r^*\)), while others may add expressivity such as the case of SPARQL , which allows a limited form of negation in expressions (e.g., \(!r\), with \(r\) being a constant or the inverse of a constant, matching any path not labelled \(r\)).

Next we define a regular path query and its evaluation.

**Regular path query**

A *regular path query* is a triple \((x,r,y)\) where \(x,y \in \con \cup \var\) and \(r\) is a path expression.

**Regular path query evaluation**

Let \(G\) denote a directed edge-labelled graph, \(c\), \(c_1\), \(c_2 \in \con\) denote constants and \(z\), \(z_1\), \(z_2 \in \var\) denote variables. Then the *evaluation of a regular path query* is defined as follows:
\begin{align*}
(c_1,r,c_2)(G) = & \{ \mu_\emptyset \mid (c_1,c_2) \in r[G] \} \\
(c,r,z)(G) = & \{ \mu \mid \dom(\mu) = \{ z \}\text{ and }(c,\mu(z)) \in r[G] \} \\
(z,r,c)(G) = & \{ \mu \mid \dom(\mu) = \{ z \}\text{ and }(\mu(z),c) \in r[G] \} \\
(z_1,r,z_2)(G) = & \{ \mu \mid \dom(\mu) = \{ z_1, z_2 \}\text{ and }(\mu(z_1),\mu(z_2)) \in r[G] \}
\end{align*}
where \(\mu_\emptyset\) denotes the empty mapping such that \(\dom(\mu) = \emptyset\) (the join identity).

**Navigational graph pattern**

If \(Q\) is a basic graph pattern, then \(Q\) is a *navigational graph pattern*. If \(Q\) is a navigational graph pattern and \((x,r,y)\) is a regular path query, then \(Q \Join (x,r,y)\) is a *navigational graph pattern*.

The definition of the evaluation of a navigational graph pattern then follows from the previous definition of a join and the definition of the evaluation of a regular path query (for a directed edge-labelled graph or a property graph, respectively). Likewise, *complex navigational graph patterns* – and their evaluation – are defined by extending this definition in the natural way with the same operators from Definition  following the same semantics seen in Definition .

### Other features

Thus far, we have discussed features that form the practical and theoretical foundation of any query language for graphs . However, specific query languages for graphs may support other features, such as aggregation (`GROUP BY`, `COUNT`, etc.), more complex filters and datatype operators (e.g., range queries on years extracted from a date), federation for querying remotely hosted graphs over the Web, languages for updating graphs, support for entailment, etc. For more information, we refer to the documentation of the respective query languages (e.g., ) and to the survey by .

### Query Interfaces

Knowledge graphs are often queried by non-expert users who may not be able to express their information needs in terms of a particular graph query language. Different types of interfaces have thus been proposed in order to assist users in querying data graphs. Such interfaces may support, for example:

**Faceted browsing:**

Users start by specifying a simple search, such as a keyword search, a type of node like `Food Festival`, or possibly other kinds of search. They are then presented with a set of matching results, and a set of facets, which are typically attributes (e.g., `venue`) and values (e.g., `Santa Lucía`) present in the current results set. Selecting a value for a facet restricts the current results set to include only results with the indicated value; this selection process can be applied iteratively to restrict results per multiple facets. Often the faceted criteria are translated into and evaluated as graph queries. Though relatively intuitive for users, such systems typically support acyclic queries that generate lists of results (analogous to graph queries that project a single variable), and rarely support more expressive queries. Examples of faceted browsing systems for graphs include VisiNav , Broccoli , SemFacet , GraFa , etc.

**Query building:**

Users are provided with a form or graphical interface that can be used to specify a graph query without needing to understand the syntax of a specific query language. Such query builders allow for incrementally adding nodes or edges to the query, assisted by features such as auto-completion, previewing intermediate results, and graph navigation. Query builders typically allow for expressing queries equivalent to (cyclic) basic graph patterns, but may not support more expressive features of query languages as described herein. Graph query builder systems include Smeagol , QueryVOWL , VIIQ , Sparklis , RDF Explorer , and more besides.

**Query-by-example:**

Users provide examples of positive and sometimes negative answers to their queries. For example, they may provide as positive examples the nodes Arica, Santiago, Viña del Mar, and as negative examples the nodes Chile, Lima, where the system will then “reverse engineer” a query that returns positive examples but not negative examples (in this case, the query proposed may return nodes of type `City` whose `country` is `Chile`). Query-by-example systems typically support basic graph patterns, and may not support more expressive querying features. They are useful in cases where users have examples of what they are looking for, but are not necessarily sure of the query they need to retrieve similar examples. Query-by-example systems for graphs include GQBE  and SPARQLByE .

**Question answering:**

Users express their queries as questions in natural language; for example, they might ask “*What food festivals will be held in Arica?*”. The question answering system will then generate answers from the graph based on its best interpretation of the question. We identify three types of question answering system. *Navigation-based systems* identify entities/nodes from the graph that are mentioned in the query, and then attempt to navigate edges from those nodes whose labels best match the question; for example, they may match the nodes Food Festival and Arica in the graph based on the question, and from there, try to navigate edges in the graph whose labels match the question in order to find answers. *Template-based systems* rather pre-suppose a fixed list of question templates expressed in the query language, with placeholder variables that will be replaced with entities/nodes detected in the question; a template matched for the previous example may be of the form “*What `X` will be held in `Y`?*”. *Translation-based systems* attempt to translate the question into a query in the structured query language, using (typically neural) machine translation techniques. The latter two types of question answering systems can additionally return a graph query that explains the answers generated. Question answering systems are often very intuitive to use, but may not always return correct results, particularly when considering complex questions/queries. Examples of question answering systems for knowledge graphs include Treo , NFF , TemplateQA , WDAqua-core1 , and more besides.

Such query interfaces enable non-expert users to formulate queries over graphs, which in turn broadens the potential impact of knowledge graphs.
