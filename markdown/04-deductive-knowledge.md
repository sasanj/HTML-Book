# Deductive Knowledge

As humans, we can *deduce* more from the data graph of Figure  than what the edges explicitly indicate. We may deduce, for example, that the Ñam festival (EID15) will be located in Santiago, even though the graph does not contain an edge . We may further deduce that the cities connected by flights must have some airport nearby, even though the graph does not contain nodes referring to these airports. In these cases, given the data as premises, and some general rules about the world that we may know *a priori*, we can use a deductive process to derive new data, allowing us to know more than what is explicitly given by the data. These types of general premises and rules, when shared by many people, form part of “*commonsense knowledge*” ; conversely, when rather shared by a few experts in an area, they form part of “*domain knowledge*”, where, for example, an expert in biology may know that *hemocyanin* is a protein containing copper that carries oxygen in the blood of some species of *Mollusca* and *Arthropoda*.

Machines, in contrast, do not have *a priori* access to such deductive faculties; rather they need to be given formal instructions, in terms of premises and *entailment regimes*, facilitating similar deductions to what a human can make. In this way, we will be making more of the meaning (i.e., *semantics*) of the graph explicit in a machine-readable format. These entailment regimes formalise the conclusions that logically follow as a consequence of a given set of premises. Once instructed in this manner, machines can (often) apply deductions with a precision, efficiency, and scale beyond human performance. These deductions may serve a range of applications, such as improving query answering, (deductive) classification, finding inconsistencies, etc. As a concrete example involving query answering, assume we are interested in knowing *the festivals located in Santiago*; we may straightforwardly express such a query as per the graph pattern shown in Figure . This query returns no results for the graph in Figure : there is no node named Festival, and nothing has (directly) the location Santiago. However, an answer (Ñam) could be automatically entailed were we to state that \(x\) being a Food Festival *entails* that \(x\) is a Festival, or that \(x\) having venue \(y\) in city \(z\) *entails* that \(x\) has location \(z\). How, then, should such entailments be captured? In Section  we already discussed how the former entailment can be captured with sub-class relations in a semantic schema; the second entailment, however, requires a more expressive entailment regime than seen thus far.

In this chapter, we discuss ways in which more complex entailments can be expressed and automated. Though we could leverage a number of logical frameworks for these purposes – such as First-Order Logic, Datalog, Prolog, Answer Set Programming, etc. – we focus on *ontologies*, which constitute a formal representation of knowledge that, importantly for us, can be represented as a graph. We then discuss how these ontologies can be formally defined, how they relate to existing logical frameworks, and how reasoning can be conducted with respect to such ontologies.

## Ontologies

To enable entailment, we must be precise about what the terms we use mean. Returning to Figure , for example, and examining the node EID16 more closely, we may begin to question how it is modelled, particularly in comparison with EID15. Both nodes – according to the class hierarchy of Figure  – are considered to be events. But what if, for example, we wish to define two pairs of start and end dates for EID16 corresponding to the different venues? Should we rather consider what takes place in each venue as a different event? What then if an event has various start and end dates in a single venue: would these also be considered as one (recurring) event, or many events? These questions are facets of a more general question: *what precisely do we mean by an “event”*? Does it happen in one contiguous time interval or can it happen many times? Does it happen in one place or can it happen in multiple? There are no “correct” answers to such questions – we may understand the term “event” in a variety of ways, and thus the answers are a matter of *convention*.

In the context of computing, an *ontology* is then a concrete, formal representation of what terms mean within the scope in which they are used (e.g., a given domain). For example, one event ontology may formally define that if an entity is an “event”, then it has precisely one venue and precisely one time instant in which it begins. Conversely, a different event ontology may define that an “event” can have multiple venues and multiple start times, etc. Each such ontology formally captures a particular perspective – a particular *convention*. Under the first ontology, for example, we could not call the Olympics an “event”, while under the second ontology we could. Likewise ontologies can guide how graph data are modelled. Under the first ontology we may split EID16 into two events. Under the second, we may elect to keep EID16 as one event with two venues. Ultimately, given that ontologies are formal representations, they can be used to automate entailment.

Like all conventions, the usefulness of an ontology depends on the level of agreement on what that ontology defines, how detailed it is, and how broadly and consistently it is adopted. Adoption of an ontology by the parties involved in one knowledge graph may lead to a consistent use of terms and consistent modelling in that knowledge graph. Agreement over multiple knowledge graphs will, in turn, enhance the interoperability of those knowledge graphs.

Amongst the most popular ontology languages used in practice are the *Web Ontology Language* (*OWL*) , recommended by the W3C and compatible with RDF graphs; and the *Open Biomedical Ontologies Format* (*OBOF*) , used mostly in the biomedical domain. Since OWL is the more widely adopted, we focus on its features, though many similar features are found in both . Before introducing such features, however, we must discuss how graphs are to be *interpreted*.

### Interpretations and models

We as humans may *interpret* the node Santiago in the data graph of Figure  as referring to the real-world city that is the capital of Chile. We may further *interpret* an edge  as stating that there are flights from the city of Arica to this city. We thus interpret the data graph as another graph – what we here call the *domain graph* – composed of real-world entities connected by real-world relations. The process of interpretation, here, involves *mapping* the nodes and edges in the data graph to nodes and edges of the domain graph.

Along these lines, we can abstractly define an *interpretation* of a data graph as being composed of two elements: a domain graph, and a mapping from the *terms* (nodes and edge-labels) of the data graph to those of the domain graph. The domain graph follows the same model as the data graph; for example, if the data graph is a directed edge-labelled graph, then so too will be the domain graph. For simplicity, we will speak of directed edge-labelled graphs and refer to the nodes of the domain graph as *entities*, and to its edges as *relations*. Given a data graph and an interpretation, while we denote nodes in the data graph by Santiago, we will denote the entity it refers to in the domain graph by Santiago (per the mapping of the given interpretation). Likewise, while we denote an edge by , we will denote the relation by  (again, per the mapping of the given interpretation). In this abstract notion of an interpretation, we do not require that Santiago or Arica be the real-world cities, nor even that the domain graph contain real-world entities and relations: an interpretation can have any domain graph and mapping.

Why is such an abstract notion of interpretation useful? The distinction between nodes/edges and entities/relations becomes important when we define the meaning of ontology features and entailment. To illustrate this distinction, if we ask whether there is an edge labelled flight between Arica and Viña del Mar for the data graph in Figure , the answer is *no*. However, if we ask if the entities Arica and Viña del Mar are connected by the relation flight, then the answer depends on what assumptions we make when interpreting the graph. Under the Closed World Assumption (CWA), if we do not have additional knowledge, then the answer is a definite *no* – since what is not known is assumed to be false. Conversely, under the Open World Assumption (OWA), we cannot be certain that this relation does not exist as this could be part of some knowledge not (yet) described by the graph. Likewise under the Unique Name Assumption (UNA), the data graph describes *at least two* flights to Santiago (since Viña del Mar and Arica are assumed to be different entities and, therefore,  and  must be different edges). Conversely, under No Unique Name Assumption (NUNA), we can only say that there is *at least one* such flight since Viña del Mar and Arica may be the same entity with two “names”.

These assumptions (or lack thereof) define which interpretations are valid, and which interpretations *satisfy* which data graphs. We call an interpretation that satisfies a data graph a *model* of that data graph. The UNA forbids interpretations that map two data terms to the same domain term. The NUNA allows such interpretations. Under the CWA, an interpretation that contains an edge  in its domain graph can only satisfy a data graph from which we can entail . Under the OWA, an interpretation containing the edge  can satisfy a data graph not entailing  so long it does not explicitly contradict that edge. OWL adopts the NUNA and OWA, which is the most general case: multiple nodes/edge-labels in the graph may refer to the same entity/relation-type (per the NUNA), and anything not entailed by the data graph is *not* assumed to be false as a consequence (per the OWA).

A graph interpretation – or simply interpretation – captures the assumptions under which the semantics of a graph can be defined. We define interpretations for directed edge-labelled graphs, though the notion extends naturally to other graph models (assuming the data and domain graphs follow the same model).

**Graph interpretation**

A *(graph) interpretation* \(I\) is defined as a pair \(I \coloneqq (\Gamma,\inp{\cdot})\) where \(\Gamma = (V_\Gamma,E_\Gamma,L_\Gamma)\) is a (directed edge-labelled) graph called the *domain graph* and \(\inp{\cdot} : \con \rightarrow V_\Gamma \cup L_\Gamma\) is a partial mapping from constants to terms in the domain graph.

We denote the domain of the mapping \(\inp{\cdot}\) by \(\textrm{dom}(\inp{\cdot})\). For interpretations under the UNA, the mapping \(\inp{\cdot}\) is required to be injective, while with no UNA (NUNA), no such requirement is necessary.

Interpretations that *satisfy* a graph are then said to be *models* of that graph.

**Graph models**

Let \(G \coloneqq (V,E,L)\) be a directed edge-labelled graph. An interpretation \(I \coloneqq (\Gamma,\inp{\cdot})\) *satisfies* \(G\) if and only if the following hold:

- \(V \cup L \subseteq \textrm{dom}(\inp{\cdot})\);
- for all \(v \in V\), it holds that \(\inp{v} \in V_\Gamma\);
- for all \(l \in L\), it holds that \(\inp{l} \in L_\Gamma\); and
- for all \((u,l,v) \in E\), it holds that \((\inp{u},\inp{l},\inp{v}) \in E_\Gamma\).

If \(I\) *satisfies* \(G\) we call \(I\) a *(graph) model* of \(G\).

### Ontology features

Beyond our base assumptions, we can associate certain patterns in the data graph with *semantic conditions* that define which interpretations satisfy it; for example, we can add a semantic condition to enforce that if our data graph contains the edge , then any edge  in the domain graph of the interpretation must also have a corresponding edge  to satisfy the data graph. These semantic conditions then form the features of an ontology language. In what follows, to aid readability, we will introduce the features of OWL using an abstract graphical notation with abbreviated terms. For details of concrete syntaxes, we rather refer to the OWL and OBOF standards . Likewise we present semantic conditions over interpretations for each feature in the same graphical format; further details of these conditions will be described later in Section .

#### Individuals

In Table , we list the main features supported by OWL for describing *individuals* (e.g., Santiago, EID16), sometimes distinguished from classes and properties. First, we can *assert* (binary) relations between individuals using edges such as . In the condition column, when we write , for example, we refer to the condition that the relation is given in the domain graph of the interpretation; if so, the interpretation satisfies the axiom. OWL further allows for defining relations to explicitly state that two terms refer to the *same* entity, where, e.g.,  states that both refer to the same region (per Section ); or that two terms refer to *different* entities, where, e.g.,  distinguishes the city from the region of the same name. We may also state that a relation does not hold using *negation*, which can be serialised as a graph using a form of reification (see Figure ).

[Table]

#### Properties

In Section , we already discussed how *sub-properties*, *domains* and *ranges* may be defined for properties. OWL allows such definitions, and further includes other features, as listed in Table . We may define a pair of properties to be *equivalent*, *inverses*, or *disjoint*. We can further define a particular property to denote a *transitive*, *symmetric*, *asymmetric*, *reflexive*, or *irreflexive* relation. We can also define the multiplicity of the relation denoted by properties, based on being *functional* (many-to-one) or *inverse-functional* (one-to-many). We may further define a *key* for a class, denoting the set of properties whose values uniquely identify the entities of that class. Without adopting a Unique Name Assumption (UNA), from these latter three features we may conclude that two or more terms refer to the same entity. Finally, we can relate a property to a *chain* (a path expression only allowing concatenation of properties) such that pairs of entities related by the chain are also related by the given property. Note that for the latter two features in Table  we require representing a list, denoted with a vertical notation ⋮; while such a list may be serialised as a graph in a number of concrete ways, OWL uses RDF lists (see Figure ).

[Table]

#### Classes

In Section , we discussed how class hierarchies can be modelled using a *sub-class* relation. OWL supports sub-classes, and many additional features, for defining and making claims about classes; these additional features are summarised in Table . Given a pair of classes, OWL allows for defining that they are *equivalent*, or *disjoint*. Thereafter, OWL provides a variety of features for defining novel classes by applying set operators on other classes, or based on conditions that the properties of its instances satisfy. First, using set operators, one can define a novel class as the *complement* of another class, the *union* or *intersection* of a list (of arbitrary length) of other classes, or as an *enumeration* of all of its instances. Second, by placing restrictions on a particular property \(p\), one can define classes whose instances are all of the entities that have: *some value* from a given class on \(p\); *all values* from a given class on \(p\); have a specific individual as a value on \(p\) (*has value*); have themselves as a reflexive value on \(p\) (*has self*); have at least, at most or exactly some number of values on \(p\) (*cardinality*); and have at least, at most or exactly some number of values on \(p\) from a given class (*qualified cardinality*). For the latter two cases, in Table , we use the notation “\(\#\{\)a\(\mid \phi \}\)” to count distinct entities satisfying \(\phi\) in the interpretation. These features can then be combined to create more complex classes, where combining the examples for Intersection and Has Self in Table  gives the definition: *self-driving taxis are taxis having themselves as a driver*.

[Table]

#### Other features

OWL supports other language features not previously discussed, including: *annotation properties*, which provide metadata about ontologies, such as versioning info; *datatype vs. object properties*, which distinguish properties that take datatype values from those that do not; and *datatype facets*, which allow for defining new datatypes by applying restrictions to existing datatypes, such as to define that places in Chile must have a *float between \(-66.0\) and \(-110.0\)* as their value for the (datatype) property latitude. For more details we refer to the OWL 2 standard . We will further discuss methodologies for the creation of ontologies in Section .

#### Models under semantic conditions

Each axiom described by the previous tables, when added to a graph, enforces some condition(s) on the models the graph. If we were to consider only the base condition of the Assertion feature in Table , for example, then the models of a graph would be any interpretation such that for every edge  in the graph, there exists a relation  in the model. Given that there may be other relations in the model (under the OWA), the number of models of any such graph is infinite. Furthermore, given that we can map multiple nodes in the graph to one entity in the model (under the NUNA), any interpretation with (for example) the relation  is a model of any graph so long as for every edge  in the graph, it holds that x = y = z = a in the interpretation (in other words, the interpretation maps everything to a). As we add axioms with their associated conditions to the graph, we restrict models for the graph; for example, considering a graph with two edges –  and  – the interpretation with , x = y = … = a is no longer a model as it breaks the condition for the irreflexive axiom. In this way, we can define a precise model-theoretic semantics for graphs based on how the aforementioned ontological features used in the graph restrict the models of that graph.

We define models under semantics conditions.

**Semantic condition**

Let \(2^G\) denote the set of all (directed edge-labelled) graphs. A *semantic condition* is a mapping \(\phi : 2^{G} \rightarrow \{ \text{true}, \text{false} \}\). An interpretation \(I \coloneqq (\Gamma,\inp{\cdot})\) is a model of \(G\) under \(\phi\) if and only if \(I\) is a model of \(G\) and \(\phi(\Gamma)\). Given a set of semantic conditions \(\Phi\), we say that \(I\) is a model of \(G\) if and only if \(I\) is a model of \(G\) and for all \(\phi \in \Phi\), \(\phi(\Gamma)\) is true.

We do not restrict the language used to define semantic conditions, but, for example, we can define the Has Value semantic condition of Table  in FOL as:

\(\forall c, p, y \Big( \big( \Gamma(c,\)prop\(,p) \wedge \Gamma(c,\)value\(,y) \big) \leftrightarrow \forall x \big( \Gamma(x,\)type\(,c) \leftrightarrow \Gamma(x,p,y) \big) \Big)\)

Here we overload \(\Gamma\) as a ternary predicate to capture the edges of \(\Gamma\). The other semantic conditions enumerated in Tables – can be defined in a similar way . This FOL formula defines an if-and-only-if version of the semantic condition for Has Value (described in Section ).

### Entailment

The conditions listed in the previous tables give rise to *entailments*, where, for example, in reference to the Symmetric feature of Table , the definition  and edge  entail the edge  according to the condition given for that feature. We now describe how these conditions lead to entailments.

We say that one graph *entails* another if and only if any model of the former graph is also a model of the latter graph. Intuitively this means that the latter graph says nothing new over the former graph and thus holds as a logical consequence of the former graph. For example, consider the graph subc. ofPlace and the graph . All models of the latter must have that , but so must all models of the former, which must have subc. ofPlace and further must satisfy the condition for Sub-class, which requires that  also hold. Hence we conclude that any model of the former graph must be a model of the latter graph, or, in other words, the former graph entails the latter graph.

We now formally define entailment under semantic conditions.

**Graph entailment**

Letting \(G_1\) and \(G_2\) denote two (directed edge-labelled) graphs, and \(\Phi\) a set of semantic conditions, we say that *\(G_1\) entails \(G_2\) under \(\Phi\)* – denoted \(G_1 \models_\Phi G_2\) – if and only if any model of \(G_1\) under \(\Phi\) is also a model of \(G_2\) under \(\Phi\).

An example of entailment is discussed in Section .

### If–then vs. if-and-only-if semantics

Consider the graph  and the graph . Both of these graphs result in the same semantic conditions being applied in the domain graph, but does one entail the other? The answer depends on the semantics applied. Considering the axioms and conditions of Tables , we can consider two semantics. Under *if*–*then* semantics – *if* **Axiom** matches the data graph *then* **Condition** holds in domain graph – the graphs do not entail each other: though both graphs give rise to the same condition, this condition is not translated back into the axioms that describe it. Conversely, under *if-and-only-if* semantics – **Axiom** matches data graph *if-and-only-if* **Condition** holds in domain graph – the graphs entail each other: both graphs give rise to the same condition, which is translated back into all possible axioms that describe it. Hence if-and-only-if semantics allows for entailing more axioms in the ontology language than if–then semantics. OWL generally applies an if-and-only-if semantics in order to enable richer entailments .

## Reasoning

Unfortunately, given two graphs, deciding if the first entails the second – per the notion of entailment we have defined and for all of the ontological features listed in Tables  – is *undecidable*: no (finite) algorithm for such entailment can exist that halts on all inputs with the correct `true`/`false` answer . However, we can provide practical reasoning algorithms for ontologies that (1) halt on any pair of input ontologies but may miss entailments, returning `false` instead of `true` in some cases, (2) always halt with the correct answer but only accept input ontologies with restricted features, or (3) only return correct answers for any pair of input ontologies but may never halt on certain inputs. Though option (3) has been explored using, e.g., theorem provers for First Order Logic (FOL) , options (1) and (2) are more commonly pursued using rules and/or Description Logics. Option (1) generally allows for more efficient and scalable reasoning algorithms and is useful where data are incomplete and having some entailments is valuable. Option (2) may be a better choice in domains – such as medical ontologies – where missing entailments may have undesirable outcomes.

### Rules

A straightforward way to provide automated access to the knowledge that can be deduced through (ontological or other forms of) entailments is through *inference rules* (or simply *rules*) encoding if–then-style consequences. A rule is composed of a *body* (if) and a *head* (then). Both the body and head are given as graph patterns. A rule indicates that if we can replace the variables of the body with terms from the data graph and form a sub-graph of a given data graph, then using the same replacement of variables in the head will yield a valid entailment. The head must typically use a subset of the variables appearing in the body to ensure that the conclusion leaves no variables unreplaced. Rules of this form correspond to (positive) Datalog  in Databases, Horn clauses  in Logic Programming, etc.

Rules can capture entailments under ontological conditions. In Table , we list some example rules for sub-class, sub-property, domain and range features ; these rules may be considered incomplete, not capturing, for example, that every class is a sub-class of itself, that every property is a sub-property of itself, etc. A more comprehensive set of rules for the OWL features of Tables  have been defined as OWL 2 RL/RDF ; these rules are likewise incomplete as such rules cannot fully capture negation (e.g., Complement), existentials (e.g., Some Values), universals (e.g., All Values), or counting (e.g., Cardinality and Qualified Cardinality). Other rule languages have, however, been proposed to support additional such features, including existentials (see, e.g., Datalog\(^\pm\) ), disjunction (see, e.g., Disjunctive Datalog ), etc.

[Table]

Rules can be leveraged for reasoning in a number of ways. *Materialisation* refers to the idea of applying rules recursively to a graph, adding the conclusions generated back to the graph until a fixpoint is reached and nothing more can be added. The materialised graph can then be treated as any other graph. Although the efficiency and scalability of materialisation can be enhanced through optimisations like Rete networks , or using distributed frameworks like MapReduce , depending on the rules and the data, the materialised graph may become unfeasibly large to manage. Another strategy is to use rules for *query rewriting*, which given a query, will automatically extend the query in order to find solutions entailed by a set of rules; for example, taking the schema graph in Figure  and the rules in Table , the (sub-)pattern ?xtypeEvent in a given input query would be rewritten to the following disjunctive pattern evaluated on the original graph:

?xtypeEvent \(\cup\) ?xtypeType \(\cup\) ?xtypePeriodic Market \(\cup\)

Figure  provides a more complete example of an ontology that is used to rewrite the query of Figure ; if evaluated over the graph of Figure , Ñam will be returned as a solution. However, not all of the aforementioned features of OWL can be supported in this manner. The OWL 2 QL profile  is a subset of OWL designed specifically for query rewriting of this form .

While rules can be used to (partially) capture ontological entailments, they can also be defined independently of an ontology language, capturing entailments for a given domain. In fact, some rules – such as the following – cannot be captured by the ontology features previously seen, as they do not support ways to infer relations from cyclical graph patterns (for computability reasons):

\(\Rightarrow\)

Various languages allow for expressing rules over graphs – independently or alongside of an ontology language – including: Notation3 (N3) , Rule Interchange Format (RIF) , Semantic Web Rule Language (SWRL) , and SPARQL Inferencing Notation (SPIN) , amongst others.

Given a graph pattern \(Q\) – be it a directed edge-labelled graph pattern per Definition  or a property graph pattern per Definition  – recall that \(\var(Q)\) denotes the variables appearing in \(Q\). We now define rules for graphs.

**Rule**

A *rule* is a pair \(R \coloneqq (B,H)\) such that \(B\) and \(H\) are graph patterns and \(\var(H) \subseteq B\). The graph pattern \(B\) is called the *body* of the rule while \(H\) is called the *head* of the rule.

This definition of a rule applies for directed edge-labelled graphs and property graphs by considering the corresponding type of graph pattern. The head is considered to be a conjunction of edges. Given a graph \(G\), a rule is *applied* by computing the mappings from the body to the graph and then using those mappings to substitute the variables in \(H\). The restriction \(\var(H) \subseteq B\) ensures that the results of this substitution is a graph, with no variables in \(H\) left unsubstituted.

**Rule application**

Given a rule \(R = (B,H)\) and a graph \(G\), we define the *application of \(R\) over \(G\)* as the graph \(R(G) \coloneqq \bigcup_{\mu \in B(G)} \mu(H)\).

Given a set of rules \(\mathcal{R} \coloneqq \{ R_1, \ldots, R_n \}\) and a knowledge graph \(G\), towards defining the set of inferences given by the rules over the graph, we denote by \(\mathcal{R}(G) \coloneqq \bigcup_{R \in \mathcal{R}} R(G)\) the union of the application of all rules of \(\mathcal{R}\) over \(G\), and we denote by \(\mathcal{R}^+(G) \coloneqq \mathcal{R}(G) \cup G\) the extension of \(G\) with respect to the application of \(\mathcal{R}\). Finally, we denote by \(\mathcal{R}^k(G)\) (for \(k \in \mathbb{N^+}\)) the recursive application of \(\mathcal{R}^+(G)\), where \(\mathcal{R}^1(G) \coloneqq \mathcal{R}^+(G)\), and \(\mathcal{R}^{i+1}(G) \coloneqq \mathcal{R}^+(\mathcal{R}^{i}(G))\). We are now ready to define the *least model*, which captures the inferences possible for \(\mathcal{R}\) over \(G\).

**Least model**

The *least model of \(\mathcal{R}\)* over \(G\)} is defined as \(\mathcal{R}^*(G) \coloneqq \bigcup_{k\in \mathbb{N}}(R^k(G))\).

At some point \(R^{k'}(G) = R^{k'+1}(G)\): the rule applications reach a fixpoint and we have the least model. Once the least model \(\mathcal{R}^*(G)\) is computed, the entailed data can be treated as any other data.

Rules can support graph entailments of the form \(G_1 \models_\Phi G_2\). We say that a set of rules \(\mathcal{R}\) is *correct* for \(\Phi\) if, for any graph \(G\), \(G \models_\Phi \mathcal{R}^*(G)\). We say that \(\mathcal{R}\) is *complete* for \(\Phi\) if, for any graph \(G\), there does not exist a graph \(G' \not\subseteq \mathcal{R}^*(G)\) such that \(G \models_\Phi G'\). Table  exemplifies a correct but incomplete set of rules for the semantic conditions of the RDFS standard .

Alternatively, rather than supporting ontology-based graph entailments, rules can be directly specified in a rule language such as Notation3 (N3) , Rule Interchange Format (RIF) , Semantic Web Rule Language (SWRL) , or SPARQL Inferencing Notation (SPIN) . Languages such as SPIN represent rules as graphs, allowing the rules of a knowledge graph to be embedded in the data graph. Taking advantage of this fact, we can then consider a form of graph entailment \(G_1 \cup \gamma(\mathcal{R}) \models_\Phi G_2\), where by \(\gamma(\mathcal{R})\) we denote the graph representation of rules \(\mathcal{R}\). If the set of rules \(\mathcal{R}\) is correct and complete for \(\Phi\), we may simply write \(G_1 \cup \gamma(\mathcal{R}) \models G_2\), indicating that \(\Phi\) captures the same semantics for \(\gamma(\mathcal{R})\) as applying the rules in \(\mathcal{R}\). Rules thus offer another form of graph entailment.

### Description Logics

Description Logics (DLs) were initially introduced as a way to formalise the meaning of *frames*  and *semantic networks* . Since semantic networks are an early version of knowledge graphs, and DLs have heavily influenced the Web Ontology Language, DLs thus hold an important place in the logical formalisation of knowledge graphs. DLs form a family of logics rather than a particular logic. Initially, DLs were restricted fragments of FOL that permit decidable reasoning tasks, such as entailment checking . Different DLs strike different balances between expressive power and computational complexity of reasoning. DLs were later extended with features beyond FOL that are useful in the context of modelling graph data, such as transitive closure, datatypes, etc.

DLs are based on three types of elements: *individuals*, such as `Santiago`; *classes* (aka *concepts*) such as `City`; and *properties* (aka *roles*) such as `flight`. DLs then allow for making claims, known as *axioms*, about these elements. *Assertional axioms* can be either unary class relations on individuals, such as `City(Santiago)`, or binary property relations on individuals, such as `flight(Santiago,Arica)`. Such axioms form the *Assertional Box* (*A-Box*). DLs further introduce logical symbols to allow for defining *class axioms* (forming the *Terminology Box*, or *T-Box* for short), and *property axioms* (forming the *Role Box*, *R-Box*); for example, the class axiom `City` \(\sqsubseteq\) `Place` states that the former class is a sub-class of the latter one, while the property axiom `flight` \(\sqsubseteq\) `connectsTo` states that the former property is a sub-property of the latter one. DLs may then introduce a rich set of logical symbols, not only for defining class and property axioms, but also defining new classes based on existing terms; as an example of the latter, we can define a class \(\exists\)`nearby`.`Airport` as the class of individuals that have some airport nearby. Noting that the symbol \(\top\) is used in DLs to denote the class of all individuals, we can then add a class axiom \(\exists\)`flight`.\(\top \sqsubseteq \exists\)`nearby`.`Airport` to state that individuals with an outgoing flight must have some airport nearby. Noting that the symbol \(\sqcup\) can be used in DL to define that a class is the union of other classes, we can further define, for example, that `Airport` \(\sqsubseteq\) `DomesticAirport` \(\sqcup\) `InternationalAirport`, i.e., that an airport is either a domestic airport or an international airport (or both).

The similarities between DL features and the OWL features seen previously are not coincidental: the OWL standard was heavily influenced by DLs, where, for example, the OWL 2 DL language is a fragment of OWL restricted so that entailment becomes decidable, where the restrictions are inspired by those defined for DLs. To exemplify a restriction, `DomesticAirport` \(\sqsubseteq ~=1\) `destination` \(\circ\) `country`.\(\top\) defines in DL syntax that domestic airports have flights destined to precisely one country (where `p` \(\circ\) `q` denotes a chain of properties). However, counting chains (in this case with \(=1~\texttt{destination} \circ \texttt{country}\)) is often disallowed in DLs to ensure decidability.

Expressive DLs support complex entailments involving existentials, universals, counting, etc. A common strategy for deciding such entailments is to reduce entailment to *satisfiability*, which decides if an ontology is consistent or not . Thereafter methods such as *tableau* can be used to check satisfiability, cautiously constructing models by completing them along similar lines to the materialisation strategy previously described, but additionally branching models in the case of disjunction, introducing new elements to represent existentials, etc. If any model is successfully “completed”, the process concludes that the original definitions are satisfiable (see, e.g., ). Due to their prohibitive computational complexity  – where for example, disjunction may lead to an exponential number of branching possibilities – such reasoning strategies are not typically applied in the case of large-scale data, though they may be useful when modelling complex domains for knowledge graphs.

A DL knowledge base consists of an A-Box, a T-Box, and an R-Box.

**DL knowledge base**

*DL knowledge base* \(\mathsf{K}\) is defined as a tuple \((\mathsf{A},\mathsf{T},\mathsf{R})\), where \(\mathsf{A}\) is the *A-Box*: a set of assertional axioms; \(\mathsf{T}\) is the *T-Box*: a set of class (aka concept/terminological) axioms; and \(\mathsf{R}\) is the *R-Box*: a set of relation (aka property/role) axioms.

Table  provides definitions for all of the constructs typically found in Description Logics. The syntax column denotes how the construct is expressed in DL. The semantics column defines the meaning of axioms using *interpretations*, which are defined in a slightly different way to those seen previously for graphs.

**DL interpretation**

A *DL interpretation* \(I\) is defined as a pair \((\inpdom,\inp{\cdot})\), where \(\inpdom\) is the *interpretation domain*, and \(\inp{\cdot}\) is the *interpretation function*. The interpretation domain is a set of individuals. The interpretation function accepts a definition of either an individual \(a\), a class \(C\), or a relation \(R\), mapping them, respectively, to an element of the domain (\(\inp{a} \in \inpdom\)), a subset of the domain (\(\inp{C} \subseteq \inpdom\)), or a set of pairs from the domain (\(\inp{R} \subseteq \inpdom \times \inpdom\)).

An interpretation \(I\) *satisfies* a knowledge-base \(\mathsf{K}\) if and only if, for all of the syntactic axioms in \(\mathsf{K}\), the corresponding semantic conditions in Table  hold for \(I\). In this case, we call \(I\) a *model* of \(\mathsf{K}\).

For \(\mathsf{K} \coloneqq (\mathsf{A},\mathsf{T},\mathsf{R})\), let:

- \(\mathsf{A} \coloneqq \{ \)`City(Arica)`, `City(Santiago)`, `flight(Arica,Santiago)`\(\}\);
- \(\mathsf{T} \coloneqq \{\)`City` \(\sqsubseteq\) `Place`, \(\exists\)`flight`\(.\top \sqsubseteq \exists\)`nearby`.`Airport`\(\} \);
- \(\mathsf{R} \coloneqq \{\)`flight` \(\sqsubseteq\) `connectsTo`\(\} \).

For \(I = (\inpdom,\inp{\cdot})\), let:

- \(\inpdom \coloneqq \{ ⚓,\,🏔,\,✈ \}\);
- `Arica`\(I\) \(\coloneqq\,⚓\), `Santiago`\(I\) \(\coloneqq\,🏔\), `AricaAirport`\(I\) \(\coloneqq\,✈\);
- `City`\(I\) \(\coloneqq \{ ⚓,\,🏔 \}\), `Airport`\(I\) \(\coloneqq \{ ✈ \}\);
- `flight`\(I\) \(\coloneqq \{ (⚓,\,🏔) \}\), `connectsTo`\(I\) \(\coloneqq \{ (⚓,\,🏔) \}\), `sells`\(I\) \(\coloneqq \{ (✈,\,☕) \}\).

The interpretation \(I\) is not a model of \(\mathsf{K}\) since it does not have that \(⚓\) is `nearby` some `Airport`, nor that \(⚓\) and \(🏔\) are in the class `Place`. However, if we *extend* the interpretation \(I\) with the following:

- `Place`\(I\) \(\coloneqq \{ ⚓,\,🏔 \}\);
- `nearby` \(\coloneqq \{ (⚓,\,✈) \}\).

Now \(I\) is a model of \(\mathsf{K}\). Note that although \(\mathsf{K}\) does not imply that `sells(AricaAirport,coffee)` while \(I\) indicates that \(✈\) does indeed sell \(☕\), \(I\) is still a model of \(\mathsf{K}\) since \(\mathsf{K}\) is not assumed to be a complete description, per the OWA.

Finally, the notion of a model gives rise to the notion of entailment, which tells us which knowledge bases hold as a logical consequence of which others.

**Entailment**

Given two DL knowledge bases \(\mathsf{K}_1\) and \(\mathsf{K}_2\), we define that \(\mathsf{K}_1\) entails \(\mathsf{K}_2\), denoted \(\mathsf{K}_1 \models \mathsf{K}_2\), if and only if any model of \(\mathsf{K}_1\) is a model of \(\mathsf{K}_2\).

Let \(\mathsf{K}_1\) denote the knowledge base \(\mathsf{K}\) from the Example , and define a second knowledge base with one assertion: \(\mathsf{K}_2 \coloneqq ( \{ \)`connectsTo`\((\)`Arica`, `Santiago`\() \}, \{\}, \{\} )\) with one assertion. Though \(\mathsf{K}_1\) does not assert this axiom, it does entail \(\mathsf{K}_2\): to be a model of \(\mathsf{K}_2\), an interpretation must have that \((\)`Arica`\(I\), `Santiago`\() \in\) `connectsTo`\(I\), but this must also be the case for any interpretation that satisfies \(\mathsf{K}_1\) since it must have that \((\)`Arica`\(I\), `Santiago`\(I\)\() \in \)`flight` and `flight` \(\subseteq\) `connectsTo`\(I\). Hence any model of \(\mathsf{K}_1\) must also be a model of \(\mathsf{K}_2\), and \(\mathsf{K}_1 \models \mathsf{K}_2\) holds.

Unfortunately, the problem of deciding entailment for knowledge bases expressed in the DL composed of the unrestricted use of all of the axioms of Table  is undecidable since we could reduce instances of the Halting Problem to such entailment. Hence DLs in practice restrict use of the features listed in Table . Different DLs apply different restrictions, implying different trade-offs for expressivity and the complexity of entailment. Most DLs are founded on one of the following base DLs (we use indentation to denote derivation):

- [\(\mathcal{ALC}\)] (\(\mathcal{A}\)ttributive \(\mathcal{L}\)anguage with \(\mathcal{C}\)omplement} ), supports atomic classes, the top and bottom classes, class intersection, class union, class negation, universal restrictions and existential restrictions. Relation and class assertions are also supported.

[\(\mathcal{S}\)] extends \(\mathcal{ALC}\) with transitive closure.

These base languages can be extended as follows:

- [\(\mathcal{H}\)] adds relation inclusion.

[\(\mathcal{R}\)] adds (limited) complex relation inclusion, relation reflexivity, relation irreflexivity, relation disjointness and the universal relation.

- [\(\mathcal{O}\)] adds (limited) nomimals.
- [\(\mathcal{I}\)] adds inverse relations.
- [\(\mathcal{F}\)] adds (limited) functional properties.

[\(\mathcal{N}\)] adds (limited) number restrictions (covering \(\mathcal{F}\) with \(\top\)).

[\(\mathcal{Q}\)] adds (limited) qualified number restrictions (covering \(\mathcal{N}\) with \(\top\)).

We use “(limited)” to indicate that such features are often only allowed under certain restrictions to ensure decidability; for example, complex relations (chains) typically cannot be combined with cardinality restrictions. DLs are then typically named per the following scheme, where \([a|b]\) denotes an alternative between \(a\) and \(b\) and \([c][d]\) denotes a concatenation \(cd\):

\[ [\mathcal{ALC}|\mathcal{S}][\mathcal{H}|\mathcal{R}][\mathcal{O}][\mathcal{I}][\mathcal{F}|\mathcal{N}|\mathcal{Q}] \]

Examples include \(\mathcal{ALCO}\), \(\mathcal{ALCHI}\), \(\mathcal{SHIF}\), \(\mathcal{SROIQ}\), etc. These languages often apply additional restrictions on class and property axioms to ensure decidability, which we do not discuss here. For further details on DLs, we refer to the recent book by .

As mentioned in the body of the survey, DLs have been very influential in the definition of OWL, where the OWL 2 DL fragment (roughly) corresponds to the DL \(\mathcal{SROIQ}\). For example, the axiom  in OWL can be translated to \(\exists\)`venue`\(.\top \sqsubseteq\) `Event`, meaning that the class of individuals with some value for `venue` (in any class) is a sub-class of the class `Event`. We leave other translations from the OWL axioms of Tables – to DL as an exercise. Note, however, that axioms like  – which given a graph such as sub-taxon ofHominini entails the edge  – cannot be expressed in DL: “`subTaxonOf` \(\sqsubseteq\ \sqsubseteq\)” is not syntactically valid. Hence only a subset of graphs can be translated into well-formed DL ontologies; we refer to the OWL standard for details .

[Table]
