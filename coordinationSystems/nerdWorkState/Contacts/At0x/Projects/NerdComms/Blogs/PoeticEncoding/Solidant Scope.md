
**  
  

![](https://lh7-us.googleusercontent.com/GgaONUMzxFLP1b7P3t6NM_OzsyH-O85VdGq8zUqOhMNgAKdDj3aKEfSPg8zBJgbMCcRlf6XeVl7O8mxwMk9N9G-VmVwvNkSLskiXIujFLkdvhpUmyoaE_B6byFrGXRZr1kCeoSt9jFDGEgq0tWblsoc)

Requirements analysis

  

|   |   |
|---|---|
|Client|DeSciWorld (DSW)|
|Project|DeSciWorld (DSW)|
|Client Contact|Joshua Bate, [joshua@desci.world](mailto:joshua@desci.world)|
|Project Manager Solidant|Luka Brazda, [Luka Solidant](mailto:luka@solidant.io)|
|Project Manager {client}|Joshua Bate, [joshua@desci.world](mailto:joshua@desci.world)|
|Target Start Date||
|Target End Date||
|Key Resources|- [DeSciWorld Whitepaper (firebasestorage.googleapis.com)](https://firebasestorage.googleapis.com/v0/b/desci-5b3ec.appspot.com/o/DeSciWorld%20Whitepaper%20(3).pdf?alt=media&token=70573b9d-6236-4b05-83b9-11d1cb97a10a)<br>    <br>- [DeSciWorldDAO/gitbook-docs (github.com)](https://github.com/DeSciWorldDAO/gitbook-docs)|

Contents

[1. Introduction 3](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.gjdgxs)

[1.1. Purpose & Objectives 3](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.30j0zll)

[1.2. Terminology 3](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.np3i95275cfn)

[1.3. Scope 4](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.1fob9te)

[Solidant Responsibilities 4](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.3znysh7)

[DSW Responsibilities 5](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.2et92p0)

[2. Requirements Analysis 5](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.tyjcwt)

[2.1. Organisation and staffing 5](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.3dy6vkm)

[2.2. Business Requirements 5](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.3g41k3cqqag6)

[2.3. Functional Requirements 6](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.svm0vvz5t0bj)

[2.4. Non-Functional Requirements 9](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.2s8eyo1)

[3. System design and architecture 10](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.3rdcrjn)

[3.1. Architecture 10](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.26in1rg)

[3.1.1. Current Index.network architecture (Indexing) 10](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.7orwr4chnshc)

[3.1.2. Current Index.network architecture (Discovery) 10](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.71zjf2avwjpm)

[3.2. Interface 11](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.iavet4vuh4xp)

[3.3. Data Design 11](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.35nkun2)

[3.4. Access control 11](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.x17howtf4ef4)

[3.5. Risks and security considerations 12](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.irfcog1vijf4)

[4. Development and Implementation 13](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.1ksv4uv)

[4.1. Tools and Technologies 13](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.44sinio)

[4.2. Development Plan 13](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.2jxsxqh)

[5. Maintenance and Support 14](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.3j2qqm3)

[5.1. Maintenance Plan 14](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.1y810tw)

[5.2. Technical Support 14](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.4i7ojhp)

[5.3. Training and Documentation 15](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.2xcytpi)

[6. Conclusion 15](https://docs.google.com/document/d/1AKLvjMu1qHNScCvmaIq1kzwp2O18bRCLung9IZLtwjk/edit?exids=71471483%2C71471477&pli=1#heading=h.1ci93xb)

  
  

1. # Introduction
    

A brief overview of what is needed

  

1. ## Purpose & Objectives 
    

DeSciWorld is a community-driven initiative aiming to facilitate the on-boarding of people

into the decentralised science (DeSci) space. By providing a suite of products and services

that engage and empower would be DeScientists, we will support a second renaissance of

science that is inclusive to all, including those peoples and ideas previously left “on the

outside” due to economic, social or cultural barriers within academia.

  

DeSci implements blockchain-based solutions such as Decentralised Autonomous

Organisations (DAOs), smart contracts, and cryptocurrencies to decentralise and

revolutionise the world of scientific research and its traditional funding and publication

landscape.

  

2. ## Terminology
    

|   |   |   |
|---|---|---|
|RefNo.|Term|Description|
|1|DSW|DeSci World.|
|2|kEngram|A structured unit or node of knowledge within a Knowledge Graph. It's a piece of information that is interconnected within a broader landscape of data, contributing to the broader understanding of a concept or topic ([src](https://github.com/DeSciWorldDAO/gitbook-docs/tree/f71dd2dd245996069e21ee358a893e49bfa9b4ec/proof-of-knowledge/kengrams), [src2](https://github.com/DeSciWorldDAO/gitbook-docs/blob/f71dd2dd245996069e21ee358a893e49bfa9b4ec/start-here/proof-of-knowledge-pok/Engram%20Starter%20Guide.md)).|
|3|Haiku|A compressed form of a kEngram, transformed using semantic compression. Its form follows the rules of the traditional Japanese poetic form with the same name.|
|4|Proof Of Knowledge|Proof of Knowledge (PoK) Is a cryptographic proof that attests to the creation of an kEngram, which is a digital representation of a unit of knowledge ([src](https://github.com/DeSciWorldDAO/gitbook-docs/blob/f71dd2dd245996069e21ee358a893e49bfa9b4ec/proof-of-knowledge/proof-of-knowledge-pok.md)).|
|5|LLM|Large language model.|
|6|RAG (Retrieval Augmented Generation)|![](https://lh7-us.googleusercontent.com/Wvr2b4Fc-D5gQ2CiSm8DIJRs1Qv_-v002EkvrE_kQMmOH5mcWcScEaLpaKrj9YEqcZ0P23vGL5V99d2NsdE8icOKg9HR9RR0VWIN7Q34xW5_ADZQxwrvwwG04gPNLyr1lJWu_XXuOQVhUJ5DzOrE4f8)<br><br>The process of optimising the output of a large language model, so it references an authoritative knowledge base outside of its training data sources before generating a response.|
|7|Semantic compression|A technique that allows for correct generalisation of terms in some given context. As a result, the same ideas can be represented using a smaller set of words.|
|8|Llama Index|Popular data framework for building LLM applications.|
|9|[Ceramic Network](https://ceramic.network/)|A decentralised data network that powers an ecosystem of interoperable Web3 applications and services. Underlying concepts similar to Kafka, but applied to Web3.|
|10|Ceramic Timestamp Service|Timestamp services aggregate events from streams to be timestamped, construct Merkle proofs, publish transactions and publish timestamp events to the Ceramic Network.|
|11|[Index Network](https://index.network/)|a composable discovery protocol built on ChromaDB, Ceramic, OpenAI and Unstructured.io.|
|12|[ChromaDB](https://www.trychroma.com/)|AI-native open-source embedding database.|
|13|[Unstructured.io](http://unstructured.io)|It efficiently extracts and transforms data into clean, consistent JSON, ready for chunking, embeddings, and integration into a vector database.|
|14|[EAS](https://docs.attest.sh/)|Ethereum Attestation Service (EAS) is an open-source infrastructure public good for making attestations on-chain or off-chain.|
|15|[EtheransOS](https://www.ethereans.app/)||
|16|Embedding|High-dimensional vectors that capture the semantic meaning of words, sentences or documents..|
|17|Vector store / Embedding database|Stores vectores / embeddings and provides vector search capabilities.|
|18|Research collective|An organisational sub-unit (sub-DAO) within the DSW DAO structure that receives funding from its parent unit. Its purpose is to provide governance and drive research within individual domains.|
|19|DID|Decentralised identifiers (DIDs) are a new type of identifier that enables verifiable, decentralised digital identity. ([src](https://www.w3.org/TR/did-core/))|
|20|Ontology|A data model that describes a knowledge domain, typically within an organisation or particular subject area, and provides context for how different entities are related.|
||||
||||

  
  
  

3. ## Scope 
    

The scope of the project and what it encompasses

### Solidant Responsibilities

|   |   |
|---|---|
|Deliverable|Description|
|Scoping and requirements analysis|Research and lock-down requirements.|
|kEngram indexing service|Accepts and transforms kEngrams and their embeddings.|
|Embedding store and discovery service|Stores embeddings and exposes search capabilities.|
|Embedding scoring service|Keep track of individual embedding scores.|
|Embedding scoring snapshot and roll-up service|Generates embedding score snapshots, builds Merkle tree and pushes Merkle roots on-chain.|
|kEngram creator incentives (smart contracts)|Enables users to claim incentives based on how often their embeddings are retrieved.|
|Governance and administration system (smart contracts)|Main DAO and sub-DAO governance (proposals, voting, etc).|
|Funding distribution component (smart contracts)|Release sub-DAO funding based on governance.|
|Subgraph|Listens for, transforms and indexes events emitted by smart contracts and makes them easily queryable.|
|Auditor liaison|Recommend, liaise and work with auditors.|

  

### DSW Responsibilities

To ensure success of the project, DSW will provide:

- Response and resolution to questions, within 3 business days
    
- Review and approvals during asset reviews, within 5 business days
    
- Delivery of kEngram creation, compression and indexing PoC
    
- Attendance of weekly project sync meeting
    

  
  

2. # Requirements Analysis
    

1. ## Organisation and staffing 
    

A list of the stakeholders and their roles in the project

  

|   |   |   |   |
|---|---|---|---|
|ORGANIZATION|Area/Domain|Team member|Contact details|
|Solidant Ltd|PMO|Luka Brazda|[Luka Solidant](mailto:luka@solidant.io)|
|Solidant Ltd|Dev|Anže Mikec|[Anže Mikec](mailto:anze@solidant.io)|
|Solidant Ltd|Advisory|Dan Pavlovič|[Dan Solidant](mailto:dan@solidant.io)|
|DeSciWorld|PMO|Joshua Bate|[joshua@desci.world](mailto:joshua@desci.world)|
|DeSciWorld||at0x|at0x@nerddao.xyz|
|DeSciWorld||Carlos DiMatteo|carlosdima97@gmail.com|
|DeSciWorld||Raquel Raigal|raquel@aoratos.io|
|DeSciWorld||Eneko|enekouru@gmail.com|

2. ## Business Requirements
    

A high-level description of functions and features that the software will provide

  

|   |   |
|---|---|
|Ref No.|Business Requirement|
|1|Anyone should be able to contribute to the DSW knowledge graph.|
|2|Anyone should be able to query the DSW knowledge graph.|
|3|Content creators should be incentivised for their contributions.|
|4|Researchers should be able to secure funding and do research by participating in research collectives (sub-DAOs).|
|5.|Funding decision-making should be driven by the community.|
|6.||
|7.||

  

3. ## Functional Requirements
    

A detailed description of the specific functions and features that the software will provide

  

|   |   |   |   |
|---|---|---|---|
|Reference No.|Functional Requirement|   |   |
|1. kEngram creation|   |   |   |
|1.1|Users should be able to input content in any form and submit it to the system.|   |   |
|1.2|Users should be required to attest to being authors of submitted data by signing with their private key (off-chain).<br><br>  <br><br>* Using EAS - Ethereum Attestation Service|   |   |
|1.3|kEngram payloads should embed the author’s DID.|   |   |
|1.4|Users should be able to reference other kEngrams.|   |   |
|1.5|Users should not be able to delete their kEngrams.|   |   |
|1.6|Users should be able to update their kEngrams by publishing revisions.<br><br>  <br>* An update creates a new entry and a reference to the previous version. This is taken care of by Ceramic natively - streams are append-only, similar in design to Kafka topics.|   |   |
|1.7|Users should be able to prove the content they authored hasn’t been tampered with.<br><br>  <br><br>* Having the ability to verify the reference to the signed attestation.|   |   |
|1.8|Users should be able to submit kEngrams through user-facing client applications, i.e. “interfaces”.<br><br>  <br><br>* Initial set of interfaces being developed by the DSW team.|   |   |
|1.9|Interfaces should be able to enforce their own ontologies - json payload schema definitions.|   |   |
|||   |   |
|2. Indexing and transformation|   |   |   |
|2.1|The system should compress incoming kEngrams into a uniform Haiku representation; transforming user content into compressed form.<br><br>  <br><br>* Semantic compression|   |   |
|2.2|The system should store original content (kEngrams) in decentralised storage (IPFS).<br><br>  <br><br>* Index.network, through Ceramic streams, persists data on IPFS ([Stream Lifecycle \| Ceramic documentation](https://developers.ceramic.network/docs/protocol/js-ceramic/streams/lifecycle#maintenance))|   |   |
|2.3|The system should store compressed representations (haikus) in decentralised storage (IPFS).|   |   |
|2.4|The system should be able to resolve original content, given its haiku representation.|   |   |
|2.5|The system should store haiku embeddings in an embedding database.<br><br>  <br><br>* Index.network uses ChromaDB as its underlying embedding database. It also pushes everything to Ceramic streams. ([index/indexer at dev · indexnetwork/index (github.com)](https://github.com/indexnetwork/index/tree/dev/indexer))|   |   |
|2.6|The system should ensure the author’s DID is propagated and stored across all kEngram representations. (kEngram -> haiku -> embedding)|   |   |
|2.7|The system should be able to resolve the author’s DID, given an embedding vector.|   |   |
|2.8|The system should push data to Index.network / Ceramic on behalf of the user.<br><br>  <br><br>* Delegation ([Authorizations \| Ceramic documentation](https://developers.ceramic.network/docs/protocol/js-ceramic/accounts/authorizations))|   |   |
|2.9|Haikus and kEngrams should live in separate indexes, to facilitate better performance.<br><br>  <br><br>* Decoupling also leads to a less opinionated implementation, which could help with future integrations.|   |   |
|2.10||   |   |
|2.11||   |   |
|2.12||   |   |
|3. Querying|   |   |   |
|3.1|Users should be able to transform text queries into embeddings (RESTful API).<br><br>- Input is text.<br>    <br>- Result is an embedding.|   |   |
|3.2|Users should be able to query embeddings (RESTful API).<br><br>- Input is an embedding.<br>    <br><br>- Returns a list of embeddings and their corresponding weights.<br>    <br><br>  <br><br>* Index.network are working on exposing their ChromaDB component.|   |   |
|3.3|Users should be able to fetch kEngrams per interface.|   |   |
|3.4|Users should be able to fetch kEngrams per research collective.|   |   |
|3.5|Users should be able to fetch kEngrams per author (EOA/DID).|   |   |
|3.6||   |   |
|4. Chat|   |   |   |
|4.1||   |   |
|5. Scoring embeddings|   |   |   |
|5.1|The system should track how often an embedding has been pulled from the vector store.|   |   |
|5.2|The system should track embedding metadata:<br><br>- Source interface<br>    <br>- Research collective<br>    <br>-|   |   |
|5.3|The system should protect against users boosting their own embeddings.|   |   |
|5.4|The system should regularly post score snapshots on-chain.  <br>  <br>* Build Merkle trees, post Merkle roots on Ethereum mainnet.<br><br>* Needs to be performant, scalable and cost-optimised.<br><br>* Option is to leverage UMA Optimistic oracles|   |   |
|5.5|Embedding scores should be decoupled from incentives amounts.|   |   |
|6. kEngram incentivisation|   |   |   |
|6.1|Users should be incentivised based on how their kEngram embedding scores.|   |   |
|6.2|Users should be able to claim incentives on-chain.<br><br>  <br><br>* Verify claims against a Merkle root (users submit leaf data and proof)<br><br>* Airdrops are discouraged - would require traversing unbounded arrays.|   |   |
|6.3|Merkle tree leaf should consist of:<br><br>- Total incentives amount<br>    <br>- Claimed incentives amount<br>    <br>- User wallet address (resolved by the smart contract)<br>    <br><br>  <br><br>Users claiming rewards would receive “total incentives amount” - “claimed incentives amount” of incentives.|   |   |
|6.4|Users should be able to fetch the total amount claimable.|   |   |
|6.5|Users should be able to fetch the total amount claimed.|   |   |
|6.6|Users should be able to fetch the total amount accrued.  <br>  <br>* Note, might be different from “claimable”; this data point is computed in (or close to) real-time, whereas “claimable” is computed at larger intervals to save on costs (constructing a Merkle tree and pushing roots on-chain incurs costs).|   |   |
|6.7||   |   |
|7. Governance and organisational structure|   |   |   |
|7.1|[gitbook-docs/dsw-dao/dao-structure.md at main · DeSciWorldDAO/gitbook-docs (github.com)](https://github.com/DeSciWorldDAO/gitbook-docs/blob/main/dsw-dao/dao-structure.md)|   |   |
|7.2|Governance should be based on DSW token distribution.|   |   |
|7.3|The org. Structure should allow for sub-organisations called sub-DAOs.|   |   |
|7.4|Sub-DAOs internal governance should wrap/be based on DSW token distribution.|   |   |
|7.5|Main governing body should be able to propose and create a new sub-DAO.|   |   |
|7.6|Main governing body should be able to propose and vote on sub-DAO funding.|   |   |
|||   |   |
|8.|   |   |   |
|||   |   |
|||   |   |
|||   |   |
|||   |   |

  

4. ## Non-Functional Requirements
    

A list of the technical, performance, and usability requirements for the software

  

|   |   |
|---|---|
|Reference No.|Non-Functional Requirement|
|1|RESTful API should be deployed on DSW’s AWS environment.|
|2|When the system is paused, the API should return an appropriate response.|
|3|All API requests should require authentication.|
|4|All smart contracts should be upgradeable.|
|5|All smart contracts should be audited.|
|6|System should have a testnet deployment in place.|
|7|All components must be designed with scalability in mind.|
|8||
|9||
|10||
|11||
|12||

3. # System design and architecture
    

1. ##  Architecture
    

An overview of the overall system architecture and its components

  

1. ### Current Index.network architecture (Indexing)
    

![](https://lh7-us.googleusercontent.com/tpVCK1Ke2jIW4brtnsQgX_vNJ5zPSgKysuaiyF7aBIiYusi77WG1aQ8YPQhFeRCt3ZPcqbnEr2X8U1enCb3Jx8H1ELG0CmxN664F4vHeusfy9rlOjpM4yZENwJqgS6SjfLsNDVCsdQMqCZwk0Hk8tFU)

* Note: Kafka consumers are being replaced with native Ceramic pub/sub integration

2. ### Current Index.network architecture (Discovery)
    

![](https://lh7-us.googleusercontent.com/pXQ7Ufzh2lzPYh20WTvCDclPHeZ3MpLhSrW-Fh6zqar9FCj2ScOYYvHfMUrPxoi9RhuEciN-4ssLYyhkGvG5UL7LmT9j8USKy9BJ0_iQkr65L_qb21qqlr0FQbCuvkDEdefRs_0PMsAWdI9BmZ9ewh8)

2. ## Interface
    

An overview of the overall system architecture and its components

  

TBD

  

|   |   |   |
|---|---|---|
|Ref No.|Interface|   |
|Endpoint|Notes|
|1.|   |   |
||||
||||
||||
||||
||||
||||
||||
||||

  

3. ## Data Design
    

A description of the data model and how it will be stored and managed

  

TBD

  

|   |   |   |   |
|---|---|---|---|
|Ref No.|Entity|   |   |
|Property|Notes|Editable|
|1||   ||
|1.1||||
|1.2||||
|1.3||||
|1.4||||
|1.5||||
|1.6||||
|1.7||||
|1.8||||
|1.9||||
|1.10||||
|1.11||||
|1.12||||

  

4. ## Access control
    

TBD

  

|   |   |   |
|---|---|---|
|Ref No.|Role|Description|
|1|||
|2|||
|3|||
|4|||
|5|||
|6|||
|7|||

  

5. ## Risks and security considerations
    

TBD  

4. # Development and Implementation
    

1. ## Tools and Technologies
    

A list of the development tools and technologies that will be used

  

|   |
|---|
|Tool|
|Foundry - [https://book.getfoundry.sh/](https://book.getfoundry.sh/)|
|Solidity - [https://soliditylang.org/](https://soliditylang.org/)|
|Subgraph - [https://thegraph.com/en/](https://thegraph.com/en/)|
|Ceramic Network - [https://ceramic.network/](https://ceramic.network/)|
|Index Network - [https://index.network/](https://index.network/)|
|Llama Index - [https://www.llamaindex.ai/](https://www.llamaindex.ai/)|
|Ethereum Attestation Service - [https://docs.attest.org/docs/welcome](https://docs.attest.org/docs/welcome)|
|UMA (Optimistic Oracle) - [https://docs.uma.xyz](https://docs.uma.xyz)|
|ChromaDB - [https://www.trychroma.com/](https://www.trychroma.com/)|
|Unstructured.io - [https://unstructured.io](https://unstructured.io)|
|EthereansOS - [https://www.ethereans.app/](https://www.ethereans.app/)|

2. ## Development Plan
    

A high-level description of the development plan, including milestones and timelines.

  

|   |   |   |
|---|---|---|
|Milestones|Responsible|Start|
|Kick Off|Solidant & DSW|20.3.2024|
|Scoping|Solidant & DSW|20.3.2024|
|Requirements Gathering|Solidant|20.3.2024|
||||
||||
||||
||||
||||
||||
||||
|System Integration|||
|User Acceptance Test|Solidant & DSW||
|Go-No-Go Checklist|Solidant & DSW||
|Post Mortem Meeting|Solidant & DSW||

5. #  Maintenance and Support
    

The specific timelines, service level agreements (SLAs), and communication protocols will be defined in a separate agreement or contract between the Service Provider and the client. This maintenance and support plan will serve as a guiding framework to ensure ongoing support, security, and continuous improvement of the DSW platform.

1. ##  Maintenance Plan 
    

The Service Provider will perform regular maintenance activities, including:

  

1. Smart Contract Audits: Conducting regular audits of the smart contracts to identify vulnerabilities, assess code quality, and provide recommendations for improvements.
    

  

2.  Patch Management: Proactively managing security patches and updates for the smart contracts, keeper sub-system, and underlying dependencies to address known vulnerabilities and emerging risks.
    

  

3.  Monitoring and Alerting: Implementing a comprehensive monitoring system to monitor the platform's health, performance, and security, and configuring real-time alerts to address anomalies, security breaches, or performance issues.
    

  

4.  Incident Response and Disaster Recovery: Maintaining an incident response plan to handle security incidents, system failures, and other unexpected events, including regular backups of critical data to facilitate disaster recovery.
    

  

5.  Updates and Upgrades: Managing system updates and upgrades for servers, databases, software dependencies to ensure compatibility, performance improvements, and security enhancements.
    

  

6. User Feedback Collection: Establishing mechanisms to collect user feedback, suggestions, and bug reports to identify areas of improvement and address any usability or functionality issues.
    

  

7. Feature Development: Incorporating user feedback and market demands to plan and develop new features, enhancements, and optimizations for the platform.
    

2. ## Technical Support 
    

The service provider will work with the client to establish effective communication channels, such as a support ticket system, email, or community forums, to facilitate user engagement, address concerns, and disseminate important platform updates.

3. ## Training and Documentation 
    

The Service Provider will maintain up-to-date documentation, including user guides, technical specifications, and API documentation, to assist platform users and internal stakeholders. Knowledge sharing sessions or training materials may be provided as needed.

6. #  Conclusion
    

TBD

**