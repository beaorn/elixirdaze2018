# ElixirDaze 2018

Notes and links from ElixirDaze 2018

[Website](http://www.elixirdaze.com/) |
[Twitter](https://twitter.com/elixirdaze) |
[#ElixirDaze2018](https://twitter.com/hashtag/elixirdaze2018?f=tweets&vertical=default&src=hash)

## Thursday

### Ben Marx - Bleacher Report - Imperfect architecture

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
  - wabi-sabi
  - growth, decay, growth
    - test, docs, standards in place to prevent large periods of decay
- introducing new technology
  - choose least worst option (works for now, periodically re-evaluate)
  - needs maintained - requires people
  - isolate experimentation

### Consistent Distributed Elixir - Chris

- everything is a process in erlang
- limit access to external resource
  - genserver for global lock
- run multiple nodes
  - GenServer.start_link with global lock
  - if node with global lock process goes away other node would start up new global process
  - if nodes cant talk due to network issue, multiple nodes could have processes that can be reached by clients
- have a way to consistently manage state in elixir other than databases
  - distributed systems - cap theorem
    - available vs consistent
    - paxos => raft
    - https://github.com/toniqsystems/raft
    - logs - linearized writes on top of rocksdb

### Deep dev into hex

- pkg manager for erlang / elixir ecosystem
- mix hex.info
  - mix hex.info packagename
  - mix hex.info packagename version
- mix hex.search term
- mix hex.docs online packagename (hex 0.17.4)
- mix hex.docs online packagename version
- mix hex.docs fetch (offline - downloads docs for all dependencies in your current app)
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
- mix hex.audit
- mix hex.publish
- mix hex.retire

### format your elixir code now
- CI run mix format --check-formated

## Friday

### hitchhikers guide to the unexpected
- property based testing
- supervisors
  - permanent, transient, temporary
  - supervisors, boot order
  - know how it boots, how it fails, supervision tree

### taking elixir to the metal
- elixir and outside world: ports, erl_interface, C Nodes, Port Drivers, NIFs
  - mix uses port to talk to git binary, jiffy uses NIF
- NIFs - Native Implemented Functions
  - Usually implemented in C\C++
  - Reasons to write
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
      - Rep an env that can host erlang terms
      - contains info about calling erlang process
    - ERL_NIF_TERM
  - Rustler - lib for writing Erlang NIFs in Rust
    - https://github.com/hansihe/rustler
    - code you write in Rust NIF should never be able to crash the Erlang VM
  - Dirty NIFs
    - allows you to call a NIF w/o worrying about blocking a normal scheduler
    - by default have same number of dirty schedulers as normal schedulers

### Build your own web framework

- Cowboy, Plug, EEx

## Raxx

- https://github.com/CrowdHailer/raxx
- HTTP interface for servers, frameworks
- Ace - server to run Raxx apps
  - HTTP/2 + HTTPS by default

## Phoenix Ecto Contexts DDD

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


## Abstract Alchemy
- https://github.com/expede/witchcraft
