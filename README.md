# ElixirDaze 2018

Notes and links from ElixirDaze 2018

[Website](http://www.elixirdaze.com/) |
[Twitter](https://twitter.com/elixirdaze) |
[#ElixirDaze2018](https://twitter.com/hashtag/elixirdaze2018?f=tweets&vertical=default&src=hash)

![daze]

[daze]: daze.jpg

## Thursday

### Keynote - [Rob Conery](https://twitter.com/robconery)

### Why Elixir Matters - [Osa Gaius](https://github.com/osagaius)

**Slides**: https://speakerdeck.com/osayame/why-elixir-matters-a-genealogy-of-functional-programming-lonestar-elixirconf-2018

- The rise in popularity of the internet and the need for non-interrupted availability of services has extended a class of problems that Erlang can solve - Joe Armstrong

- Explained History of functional programming including Lambda Calculus in the 30's, Lisp in 1960, Scheme in 1970, and Erlang in 1986 etc.

### Making Noise - [Ben Marx](https://github.com/bgmarx)

- API Gateway Pros - Simple, Secure
- API Gateway single point of failure
  - change things in isolation not everything at once
  - coupled service dependencies
  - app fallback logic / how to function in degraded state
    - Task.await vs Task.yield for async calls
      - Task.await - known you want it to crash if error happens
      - Task.yield - use for unpredictable scenarios (3rd party)
    - prevent all or nothing response if possible, incremental response (progressive API design)
  - Fastly / varnish intercept and reduce load on actual gateway to make
    it more resilient for things not needed to go directly through gateway
- architecture - systems and people
  - system lifecycle
  - Wabi-Sabi - basically implies that things can be imperfect and unfinished, yet still beautiful
  - Systems
    - Plan > Code > QA > Deploy -- this doesn't actually work in life or in software development
    - Instead, you introduce, evaluate, adjust, and start again.
    - App Fallback Logic -- Things fall apart, not realistic for things to work at all times,
    - Using task.yield instead of task.await
  - People
    - People lifecycle uses introduce > evaluate > adjust schedule
    - Trusted Autonomy - Trust but Verify
    - Bidirectional Ideas
  - Using kafka with erlang drivers and elixer driver
    - elixir driver crashed server
    - didn't know whether to fix driver or switch services or what
    - ended up building ruby library
  - growth, decay, growth
    - test, docs, standards in place to prevent large periods of decay
    - Embrace idea of symmetry and dissonance and whatever you do now will decay in so many months
- introducing new technology
  - choose least worst option (works for now, periodically re-evaluate)
  - needs maintained - requires people
  - isolate experimentation

### Consistent Distributed Elixir - [Chris Keathley](https://github.com/keathley)

**Raft**: https://github.com/toniqsystems/raft

- everything is a process in erlang
- limit access to external resource
  - genserver for global lock
- run multiple nodes
  - GenServer.start_link with global lock
  - if node with global lock process goes away other node would start up new global process
  - if nodes cant talk due to network issue, multiple nodes could have processes that can be reached by clients
- have a way to consistently manage state in elixir other than databases
  - distributed systems - cap theorem
    - When a network is partitioned you can either be available or consistent
    - Some problems need consistency
    - available vs consistent
      - Available
        - Every request receives a response without guarantee that it contains the most recent write
      - Consistent
        - Every read receives the most recent write but may not always receive a response
      - AP - Available during partitions
      - CP - Consistent during partitions
    - paxos => raft
    - logs - linearized writes on top of rocksdb, replicated across all nodes
    - Leader/follower distributed system design

### Crypto + Concurrency - [Anna Neyzberg](https://twitter.com/ANeyzb)

- Big idea behind cryptocurrencies is a digital transfer over internet that isnt back by hard collateral
- semi anonymous and secure
- Cryptography
  - bitcoin uses proof-of-work from hashcash(earliest form of digital currency)
- How dows bitcoin work?
  - a decentralized cluster of nodes
  - each node has its own copy of the blockchain
  - blockchain is just ledger of transactions
  - each data point points to the previous data point
- How does relate to Elixir?
  - decentralized ledger
  - processes - message processing
    - light weight
    - ways to communicate between processes
      - send
      - receive
    - agents - agents manage state

### Deep Dive into Hex - [Todd Resudek](https://github.com/supersimple)

**Slides**: https://github.com/supersimple/deep-dive-into-hex-slides

- Hex is the pkg manager for erlang / elixir ecosystem
  - It is analagous to NPM for Node.js, or Gems for Ruby
  - Hex has about 6000 packages available compared to RubyGems 9200
  - Hex allows you to work fawster, prevent issues, debug quicker
- Available Packages
  - Hex - 6000
  - Rugy Games - 9200
  - Crates - 14000
  - NPM - 500000
- mix hex.info
  - mix hex.info packagename
  - mix hex.info packagename version
- mix hex.search term
  - finds packages
- mix hex.docs online packagename (hex 0.17.4)
  - loads up documentation for packagename
  - only compatible with > 0.17
- mix hex.docs online packagename version
- mix hex.docs fetch
  - (offline - downloads docs for all dependencies in your current app)
- mix hex.docs fetch packagename (outside of app)
- mix hex.docs offline packagename (will download if it isnt already fetched)
- use -latest flag to override
- mix hex.user register
  - mix hex.user reset_password local
  - mix hex.user reset_password account (hex.pm user)
  - mix hex.user key -lost
  - mix hex.user key -revoke keyname
  - mix hex.user key --revoke-all
  - mix hex.auth
  - mix hex.config
  - mix hex.config key
- mix hex.outdated (shows current, latest, update, update possible)
  - --all will list all
- mix hex.audit
  - shows retired packages that you have installed as dependencies
- mix hex.publish
  - can publish privately
  - watch out for pre-release dependencies
- mix hex.retire
  - signifies a package is no longer recommended
  - retirement must include a valid reason

### Format Your Elixir Code Now - [Jake Worth](https://github.com/jwworth)

**Slides**: https://speakerdeck.com/jwworth/format-your-elixir-code-now

- Linting vs Autoformatting
  - Linter - recommends changes
  - Autoformatter - recommends/changes code
- languages provide a lot of different ways to do the same thing
- Pros of Autoformatting
  - productivity
    - gives more time for important decisions
  - consistency
    - friendliness to new comers to your code
    - helps relieve workload on manual code reviews
  - collective ownership
- Negs of Autoformatting
  - disregards any particular programmers preferred coding style
- Autoformatter for Elixir requireed after 1.6, forcing uniform code
- Distinct style guides separate us
  - Elixir should have consistent style
  - We need collective ownership
- CI run mix format --check-formated

### Is Security on Your Nerves? - [Paul Rogers]

  - Security between Http based communication
  - Https does not secure your network calls to be safe from ManInTheMiddle Attacks
  - RSA
    - Both client and server have a piece of the puzzle besides the password and username
    - These pieces are not sent over the http request but instead used for calculations to determine if the other side is who they say they are.


### Hardware without Hardware or Lightning without Lightning - [Amos King](https://github.com/adkron)

**Slides**: https://github.com/BinaryNoggin/lightning_without_lightning

**Code**: https://github.com/adkron/grovepi/tree/lightning-sensor

## Friday

### Keynote: The Hitchhiker’s Guide to the Unexpected - [Fred Hebert](https://github.com/ferd)

- property based testing
- supervisors
  - permanent, transient, temporary
  - supervisors, boot order
  - know how it boots, how it fails, supervision tree

### Taking Elixir to the Metal - [Sonny Scroggin](https://github.com/scrogson)

**Rustler**: https://github.com/hansihe/rustler

- elixir and outside world: ports, erl_interface, C Nodes, Port Drivers, NIFs
  - mix uses port to talk to git binary, jiffy uses NIF
  - Erlangs inet_drv port driver for networking
  - Jiffy JSON encoding/decoding
    - really fast json encoder
    - written in C
- NIFs - Native Implemented Functions
  - Usually implemented in C\C++
  - Reasons to write
    - Erlang was not designed for raw CPU throughput
    - Deal directly hardware
    - Interop w/ graphics
    - Functionality already exists in C code already exosts
  - called just like any erlang/elixir code
  - NIFs live in dynamically loaded libraries
  - NIFs replace erlang/elixir functions of the same name at module load time
  - Functionality
    - Read and write erlang terms
    - binaries
    - resource objects
    - lengthy work from NIF degrades resp of VM
    - Dont block the schedulers
      - blocked scheduler can't run other processess
      - NIF should never take over a scheduler for more than 1ms
      - options
        - chunking - repeated calls to nif
        - rescheduling (not in rustler at this time)
        - threaded NIFs
    - NIF Resource Objects
      - only real usage is to pass it back as an arg to a NIF
      - safe way to return pointers to native data structures
  - erl_nif.h in C
    - ErlEnv
      - Passed as the first argument to all NIFs
      - Represents an environment that can host erlang terms
      - contains info about calling erlang process
      - All terms in an environment are valid as long as the environment is valid
    - ERL_NIF_TERM
  - Rustler - lib for writing Erlang NIFs in Rust
    - https://github.com/hansihe/rustler
    - Handles encoding and decoding of erlang tems
    - Catches rust panics beffore they unwind to C
    - Provides faciliities for generating the bolilerplate for interacting with the Erlang VM
    - code you write in Rust NIF should never be able to crash the Erlang VM
  - Nif Resource Objects
    - safe way t oreturn pointers to native data strctures from a nIF
    - Can be stored and passed between processes on the same code
    - The only real end usage is to pass it back as an argument to a NIF
  - Rescheduling
    - Erlang NIF API provides a way for a NIF to reschedule itself directly with enif_schedule_nif
    - Combined with rustler::schedule::consume_timeslice allows all chunking to be done directly in the NIF
    - Not yet supported in Rustler due to safety conc=rns.
  - Thread NIFs
    - Spawn a separate OS thread to do the work
    - Send the result as a message to the calling process.
  - Dirty NIFs
    - allows you to call a NIF w/o worrying about blocking a normal scheduler
    - by default have same number of dirty schedulers as normal schedulers

### Build your own web framework in Elixir - [German Velasco](https://github.com/germsvel)

**Code**: https://github.com/germsvel/daze

- Cowboy, Plug, EEx
- Basics
  - really quick step by step of getting started with Cowboy.
  - have to add plugs for routing and logging get requests etc
- Templates with EEx
  - require EEx
  - compiles template to a function by passing the template and variables
  - comes with smart engine out of the box
- Handling Forms
  - use plug parsers and define what kind of parser you want, (urlencoded, multipart)
  - connection construct has query params key, body params key, and params key
  - define the route and module
  - define all function to retrieve all posts
  - throw into supervision tree
- Sessions
  - Use Plug.session with cookie and a key for the cookie and define a signing salt
  - In order to access session, need secret_key_base
- Static Assets
  - use Plug.Static
  - going to look for static asset, if cant find it, it goes throught other plugs
  - can limit directories to look with only:
  - priv/static/css

### Purify your web development with Raxx - [Peter Saxton](https://github.com/CrowdHailer)

**Talk**: http://crowdhailer.me/2018-01-17/simple-web-services-with-ace/  
**Raxx**: https://github.com/CrowdHailer/raxx  
**Ace**: https://github.com/CrowdHailer/Ace

- HTTP interface for servers, frameworks
- Ace - server to run Raxx apps
  - HTTP/2 + HTTPS by default

### Building beautiful systems with Phoenix contexts and Domain-Driven Design - [Andrew Hao](https://github.com/andrewhao)

**Slides**: https://speakerdeck.com/andrewhao/building-beautiful-systems-with-phoenix-contexts-and-ddd

- High Cohesion
  - Similar concepts belong together in code
- Loose Coupling
  - minimal dependencies on external systems
- Contexts
  - Elixir modules that group system functionality
  - mix phx.gen.html Identity User users name:string email:string
    - lib/my_app/identity/identity.ex
    - All web concerns live in web context
  - DDD
    - Design your software systems according your business domains by paying attention to the language you speak in the business
    - Context mapping
      - Get everyone in the room, put up on a wall
        - Nouns - entities
        - Verbs - events
      - Group like concepts and actions together
      - Core domain - primary area of focus of your business
      - Supporting Domain - subdomain - areas that play roles in makine the core domain happen
        - natural groupings will emerge
      - Ubiquitous language, make a glossary, consistently use same terms in code and in discussion
      - Anti-Corruption Layer
      - Aggregates
      - Publish facts (domain events) over a bus
        - Interested contexts can subscribe
        - https://github.com/otobus/event_bus


### Abstract Alchemy: Monads, ADTs, and Parallelized Pipes - [Brooklyn Zelenka](https://github.com/expede)

**Witchcraft** - https://github.com/expede/witchcraft
