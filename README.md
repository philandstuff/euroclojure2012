
# Sam & Jeff, Overtone

- Overtone - live music programming in Clojure
- clojure/conj talk was motivation, this talk is practicality
- once again, notes don't do the talk justice
- dubstep tubular bells with fancy visualization effects :D

# Stefan Tilkov, OOP -> Clojure

- OOP thinking:
  - model domains with classes & interfaces
  - encapsulate data in objects
  - prefer specific over generic solutions
- namespaces -- more than packages?
  - provide support for handling var name clashes
  - dynamic reloading
  - encapsulation
- data
  - maps, records, types
  - functions, multimethods, protocols (but forget protocols for the
    moment)
  - compare class Point { int x,y; /* etc */ } with [3 4]
  - immutable!
  - reusable!
- functions
  - if you stick to simple data, clojure has a huge number of
    functions which will Just Work on your data
- closures
  - objects-lite
- dsls
- multimethods
  - problems we are trying to solve in OO methods:
    - "global" state
    - coarse-grained re-use
    - simple-minded dispatch
- defrecord
  - still most of the benefits of maps
  - convenience functions
  - protocol support
  - platform integration
  - better performance (but the ever-present perf question: in what
    situations?)
- deftype
  - low-level stuff
  - best performance
  - last resort
- defprotocol
  - collection of functions dispatching on a common data structure
  - records & types can participate in protocols
  - can define type first, then create abstraction with protocol later
    - can even extend types from other libraries with your own protocols
  - performance, grouping, platform integration
  - limited dispatch compared to multimethods
- roadmap recommendation, oop -> functional
  - 1. namespaces, functions, persistent data structures
  - 2. multimethods
  - 3. records & protocols
  - 4. deftype (as said, last resort)

# Max Weber, Current state vs. (eve)nt sourcing

- Motivational example: online shop
  - track contents of shopping cart
  - user adds clojure book, then earl-grey tea, then removes earl-grey
    tea
  - don't want to forget that the tea was ever in the basket, so that
    we can promote tea-related products in future
- change in data model: store sequences of events rather than current
  state
  - so in above example, three events:
    - add book
    - add tea
    - remove tea
  - don't lose the historical fact that tea was in basket for a while
- events are immutable facts
  - don't rewrite history!
- types of interaction: reads vs writes
  - reads
    - does it matter if it's the latest state of the data? often no
  - writes
- leads to: CQRS
  - from CQS: object-level command/query separation
  - CQRS: extends this to whole architecture
- a command is a potential event, which may yet be modified or
  rejected
- aggregates:
- command handler: translates commands into final events (is this
  right?)
- eve library - convenience functions for implementing event sourcing
  and CQRS
- Q&A
  - failure scenario handling?
    - we are planning to use avout/zookeeper to handle this

# Edmund Jackson, core.logic.intro

- quick intro: functions vs predicates
  - functions:
    - (cons :a []) => [:a]
    - (cons :a [:b]) => [:a :b]
  - predicates:
    - `(conso :a []   [:a]   ) => #s ; ie success`
    - `(conso :b [:a] [:b :a]) => #s`
    - `(conso :c [:a] [:b :a]) => #f ; ie failure`
- logic variables: symbols for "whatever" rather than concrete values
  - eg:

    (run* [q]
        (conso q [] [:a]))
    => q -> :a

    (run* [q]
        (conso q [] []))
    => q -> []

- composition
  - conjunction:

    (run* [q]
        (membero q [:a :b :c])
        (membero q [:b :c :d]))
    => q -> [:b :c]

  - disjunction:
    - uses conde
  - equality
    - == (unification)
  - inequality
    - != (nonunification?)
  - existential quantification?
    - fresh
- logic programming as a db query language (for an abstract idea of
  db)


# Hakan Raberg, Jon Pither: Reducing the Middle Office

- System: lifecycle management of derivatives
  - 1M lines of java, 6 years old, 30+ integration points
  - distributed team - 5 different countries, 40 people
- Requirement: system must stay alive. Therefore:
  - no rewrite possible
  - no moving to "maintenance mode"
- Problem:
  - system talks to larger back office system
  - text based DSL rule engine - 100k LOC
  - this layer large source of waste and risk
- Approach
  - avoid a big bang rewrite
  - clojure is a means, not an end, so don't make it part of the sales
    pitch
  - parallel effort to split system
- Details
  - Elasticsearch to free data (what is elasticsearch?)
  - Clojure to free logic
  - Reconciliation with back-office systems using Clojure concurrency
- Changing Culture
  - "Is emacs really necessary?"
  - "What can you do in Clojure you cannot do in Java?"
  - "How do we model this without classes?"

# Zach Tellman, Generating and Analyzing Events

- What do we lose from traditional logging?
  - log events, but don't log when events happen..
- What is an event?
  - async-result
- CSP-style model of channels and processes
- lamina.viz provides visualization of your topology

# Filip de Waard, Building a Search Engine with Clojure

- crawling approaches
  - brute force
  - directed (specify where crawler goes)
  - weighted (specify based on score)
- considerations
  - respect robots.txt
  - delay between reqs
  - deal with anti-crawling measures
- directed crawl example
  - different directions at different levels eg
    - "find links ending in /en on first page"
    - "find links in div#content on these pages"
    - don't follow any links on those pages
- weighted crawling
- Data extraction
  - Enlive
  - Becomes tricky when you don't know structure in advance -- need
    machine learning techniques!
- processing crawl data

# Paul Ingles, Users as Data

- reference to @mmcgrana's Logs as Data
- Everything is data!
  - code is data
  - data is data
  - builds as data
- logs as data?
  - logs as files D:
- log crunching is event processing
- users as data

# Stuart Halloway, Evident Code, at Scale

- declarative programming
  - focusing here on what rather than how
  - rock-paper-scissors example
  - map {:rock :scissors etc}
    vs set of vectors #{[:rock :scissors] etc}
  - minimizing "how":
    - functional beats imperative
    - logical beats functional
    - structure matters
- abstraction
  - focussing here on "reducing information"
  - epochal time model: action side vs perception side
    - abstraction of actions reduces information you need to provide
      - a Good Thing
    - abstraction of perception reduces information you can get
      - a Bad Thing, apparently
      - compare encapsulating data in Java
  - consider Map<K,V>:
    - too big
    - has both commands and queries
    - has equals and hashCode
  - from datomic, a smaller interface:
    - Entity { Object get (Object); Set keySet(); }
  - ideal number of methods in an interface: 0
  - abstractions in Datomic
    - about 50 protocols/interfaces
    - action examples
      - cluster
      - storage
    - perception examples
      - database
      - entity
  - abstraction guidelines
    - fine grained
    - separate perception and action
    - don't pour concrete (not sure what this means)
- DB queries
  - pattern: getting related objects with a query
    - reason #1: prevent N+1 queries
    - reason #2: world might change between initial query and query
      for related objects
  - but: complects queries together (perhaps even save+fetch)
- data is programmable
  - SQL injection comes from writing code which writes SQL
    - and SQL isn't stored as data, it's stored as strings
    - boo :(
- schemas are data
- navigation is data
  - evil enhancement to Entity above:
    - Object reverseGet(Object key);
  - instead of separate reverseGet, provide key to get method which
    goes reverse direction
  - (get-in jack [:town :_town])
    - gets everybody who has the same town as jack
- Datalog in 6 minutes
  - I don't have a hope of taking notes for this section
  - [entity attribute value]
  - eg [42 :email jdoe@example.com]
  - eg [42 :order 107]
  - can use logic variables in any place in these 3-tuples
  - implicit join where same logic variable appears in multiple tuples
  - predicates:
    - (< 50 ?price)
  - functions:
    - [(shipping ?zip ?wieght) ?cost] ; bind inputs, get output
- evident code. Datalog was example.
- what's worse than a global variable?
  - an ambient global variable
  - ie a global you don't even mention anymore.
  - this is one reason Perl exceptions suck -- $@ is exactly this
  - database is exactly this. we never mention which database we're
    using in an SQL query.
  - by making db explicit, datomic allows joins across different
    databases
- generative testing
  - generate test data
  - generation separated from validation
  - rather than hand-curated test-cases
  - ie we say "addition is commutative" rather than "1 + 2 == 2 + 1"
- test inputs and outputs are data



# Rich Hickey, The Design of Datomic

- Problems it is trying to solve:
  - complexity
    - out of the tar pit (moseley and Marks (2006))
    - complexity caused by state and control
  - Declarative programming:
    - more oriented with *what*, less oriented with *how*
  - "Over there"
    - fear of round trips
    - fear of overloading server
  - Consistency and scale - what possibilities are there?
    - distributed systems but avoiding eventual consistency
  - Flexibility
    - sparse data, irregular data
  - Time
  - Perception and reaction
    - no polling
    - consistent
- Solutions
  - client-server architecture
    - but clients can remember
  - the database as an expanding value
    - the past doesn't change, but the future can be added
  - process
  - declarative programming
    - move query into applications, and use datalog
  - "Over there"
    - move the data to storage service
      - local disks cause problems, and they no longer provide huge benefits
    - no privileged access
    - no dedicated accessors/compute
    - app servers are peers
  - consistency and scale
    - separate reads and writes
    - turn consistency/scale tradeof into two: can scale reads
      independently from writes
    - transactions for writes
    - immutability for reads
  - Flexibility:
    - don't trade tables for documents!
      - documents are inflexible
    - remove structure
      - a la RDF
      - but with time values too
    - minimal fact: entity attribute value time
  - Time
    - transactions are serialized, datoms include tx
    - transactions are first-class: ie they are facts
      - facts about transactions include time when they occured
  - Perception & Reaction
    - push process
    - query process
    - before/after db values
- Implementation
  - Architecture
    - 3 systems: app server, transactor, storage service
  - State
    - definition: database gives you "leverage" over data
      - therefore filesystem is not database
      - neither is key-value store
    - immutable expanding value: sorted set of facts
    - maintaining sort live in storage - bad
      - bigtable: mem + storage merge (eh?)
      - occasional merge into storage
      - persistent (in both senses) trees
  - Storage
    - log (journal) of tx asserts/retracts (in tree)
    - various covering indices
    - storage requirements
      - data segment values (eh?)
      - atoms (consistent read)
      - pods (conditional put)
  - Index storage
    - very high branching factor tree
  - DB value:
    - eventual haircut
  - process
    - assert/retract can't express transformation
    - compound transactions of many facts expand until they reach
      individual asserts/retract
  - Transactor
    - accepts transactions
    - expands
  - declarative programming
    - embedded datalog
  - "Over there"
    - over here instead
    - peers directly access storage service
    - and have own query engine
    - and have live mem index and merging
    - two-tier cache
      - segments (compressed form, on/off heap)
      - datoms
  - consistency and scale
    - process/writes go through transactor
    - immutability supports consistent reads without transactions
    - query scales with peers
  - flexibility
    - attributes are the only schema
  - time
    - db is a value
  - perception and reaction
- summary
  - clojure state model, finished
    - incorporating time + durability
  
# Christophe Grand, Not So Homoiconic

- if clojure is homoiconic, why do we have so much source-as-text to
  source-as-text transformations?
- macros are a start
  - but only works for the compiler
- what you see is not what the reader sees
  - homoiconicity is designed for the compiler, not for the user
- "The reader ate my layout"
  - indentation/whitespace
  - comments
  - maps and set orderings
  - metadata
  - ::autoresolving/keywords
  - reader macros - ` ~ ~@
  - #(%1 %2)
  - @#'
  - 0xffff vs decimal equivalent
- Spoilt devs!
  - macro transformations are easy
  - we want structural transformations to be as easy
- we need to admit we have a view-update problem
  - originally a DB term
- transform source -> target, edit target, transform updated target ->
  updated source
  - but read is not bijective for reasons stated above
  - so we fake it by recombining old source with new target
- "lenses"
  - composable
  - bidirectional - get/putback
  - this might be overthinking things, but it's interesting
  - the "get" fn won't change, it's the reader. no need for lenses and
    lens combinators
  - "putback" is the important idea here
- master plan
  - source -> parse tree           - DONE! :D
  - parse tree -> expr + tx log    - EASY! :D
  - expr -> expr2                  - USER! not my problem! :D
  - expr2 + tx log -> parse tree 2 - putback - this is the real problem
  - parse tree 2 -> source 2       - EASY! :D
- parse tree != sexpr
  - parse tree includes whitespace, among other things
- tx log
  - for each node/location of parse-tree, remember corresponding
    expression
  - we need to be able to scan tx log to find closest parse-tree node
    corresponding to an expr
  - when not found, render expression using default algorithm eg
    pprint
- decouple layout transformations from structural transformations
- demo time!
  - in counterclockwise!
  - this is bloody brilliant.
- WIP
  - similarity heuristics to recover more layout on updated nodes
  - IDE integration
- Q&A
  - are you aware of operational transforms?
    - yes, they are problematic for reasons I don't understand
  - could I use this on my own external DSLs?
    - have no idea, give it a go!


