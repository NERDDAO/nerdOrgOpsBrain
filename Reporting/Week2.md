**Forwarded from [Eneko Uruñuela](https://t.me/eurunuela)**

# Reducing embedding dimensions
Using larger embeddings, for example storing them in a vector store for retrieval, generally costs more and consumes more compute, memory and storage than using smaller embeddings.

Both of our new embedding models were trained with a technique that allows developers to trade-off performance and cost of using embeddings. Specifically, developers can shorten embeddings (i.e. remove some numbers from the end of the sequence) without the embedding losing its concept-representing properties by passing in the dimensions API parameter. For example, on the MTEB benchmark, a text-embedding-3-large embedding can be shortened to a size of 256 while still outperforming an unshortened text-embedding-ada-002 embedding with a size of 1536. You can read more about how changing the dimensions impacts performance in our embeddings v3 launch blog post.

In general, using the dimensions parameter when creating the embedding is the suggested approach. In certain cases, you may need to change the embedding dimension after you generate it.

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

need to make a markatang plan for the nerds

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**

#funkmasterFlex

https://watcher.synthetix.io/markets

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
so these are all the markets on synthetix

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex

plenty to start with

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex

The way I'd organize a UI would be categorized by top pairs like ETH/BTC/SOL/LINK/DOGE

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex

And the others would be categorized by the base asset ie 

**BTC pairs** and then you can trade either side of all the available BTC pairs 

**ETH Pairs** 

**SOL Pairs** 

etc..

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
To be honest I'd be fine building on top of another liquidity source and see if there is demand

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex

to get around the oracle stuff I originally thought about the offchain keeper system or flash loans but I also don't know how difficult that is

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex

and to be honest crypto is so fee insensitive that we could make a good UI and charge a fee on top and people would still use if it's good

***

**Forwarded from [cdds](https://t.me/carlosdimatteo)**

Updates:

Locastic:

Carolina has sent a lot of the requested data, i think the team may still be off i havent gotten any updates from them


progress on project selector field for both the event & job forms

Solidant : 

Carlos missed this weeks meet cause travelling , mickey sent this as the summary:

at0x looks into setting up a working integration with index.network

Mickey and Dan have a brainstorming session re. scoring et.al on Wednesday

Mickey and at0x go through the current working build on Wednesday (late evening CET)

everybody have a look at the DSW requirements document and pitch in with comments

nerdDAO: 

slow week 

meeting with lemonade resulted in green light for collaboration but deadlines are tight and Ecuadors electricity issues are impacting

continuing work on index for the frame integration for lemonade

Internal: 

Eneko:

Lead the project On LLM Metrics (we want to have a "propietary" way of measuring response quality )

start building a rubric with questions 

freebase and conceptnet is a potential direction to follow to create a metric system 

Carlos: 

aggregation backend  (still pending)

take a look into eneko's chatbot deployment issue with mystMD https://github.com/eurunuela/phd

check the solidant scope doc more deeply

**Forwarded from [Eneko Uruñuela](https://t.me/eurunuela)**

Eneko and Carlos At0x have been discussing the possibility of writing a paper about PoK in the near future

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

Power out rn this will get processed in a couple hours

***

**Forwarded from [Caro](https://t.me/Gingerheart86)**

I want to participate!

***

**Forwarded from [Caro](https://t.me/Gingerheart86)**

Cuáles son los posibles impactos sociales de aplicar PoK para la monetización del conocimiento?  Usar como variables regiones del mundo, áreas de trabajo y conocimiento, teniendo en cuenta este último informe del Foro Económico Mundial. También resumir el informe, extraer los principales datos,  conclusiones y figuras. Hacerlo en inglés y en español.

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex

So the reason I ask is because I have a "protocol" sort of dex idea that I've been thinking about

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
Trading pairs like ETH/BTC ETH/SOL whatever on leverage

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
Could be synthetic or auction

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
Marketing would be insane

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
Not new idea but easy UI for it. Push the conversations on all the pairs on twitter. Would just need to integrate one of the synthetics/perps protocols for liquidity but I wanted to ask you if you’ve know anything related to auction based models. We did some of this at index but if we have enough market makers (or us) providing liquidity through flash loans I think we could just have a keeper auction. Sort of above my level but want to start research a bit

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**
#funkmasterFlex
The problem is that nobody knows what to build with that stuff haha same w chain link functions

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
Yeah exactly because you can run all these trades on aave but it's hard to do and not easy to manage

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**
#funkmasterFlex
So as a summary:

-> offchain dark pool with offchain attestations

-> automation for synthetic trading (just in time funds) complex routing

-> L2 synthetics/Onchain perp aggregation system

***

**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**
#funkmasterFlex
https://gov.indexcoop.com/t/iipxx-launch-the-eth-btc-ratio-index/4503

***
#funkmasterFlex
**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**

Hey man hope all is good. I'm an advisor to a project and had a question if you have a chance

Basically, we want to aggregate a bunch of different synthetic or dex assets and use flash loans (loan within the same block) to market make across lots of different exchanges 

Does layerzero have any examples of projects using keeper bots or offchain aggregation to make markets?

#funkmasterFlex

**Forwarded from [Bill | LayerZero](https://t.me/lzBill)**

yo yooo, so LZ doesnt have examples of this and we dont have automation bots or the ability to bring data on chain. id say this is more up CL or gelato's ally as opposed to us

***
#funkmasterFlex
**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**

You can find some more explanation on the peg calculation here https://docs.google.com/document/d/1hnnV-VQhztEn-YFUQhkcPQm9PbDLVfmApNVOCAnho80/edit?usp=sharing

***
#funkmasterFlex
**Forwarded from [David - Truflation](https://t.me/funkmasterflex)**

Hey man, so I think it’s gonna be a no from Chainlink. They’re really only giving grants for infra and public goods. I still think it would be a pretty dope protocol to build. I think all the offchain routing stuff would be unnecessary in an mvp. Instead I think just building on top of  synthetix or uniswap liquidity would work. Long one side short the other. 

What do you think? Would you guys be down to help build it? We can split the equity or token or protocol revenue

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

h

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

tendremos muchos safe tokens el martes

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

pero hay que hacer que firmen aka encontrar cadaveres del bear market

**Forwarded from [mattimouse](https://t.me/mattim0use)**

Wanted to learn how to make a factory contract

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

Lotta questions

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

Just feels like there’s a bunch of interesting use cases for a uniswap style factory for tokens n things

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

Currently computer off because no power messages will be parsed shortly

***

**Forwarded from [Raquel Raigal](https://t.me/rraigal)**

Plan del dia: 

- Familarizarme más con frog de forma estructurada, y testear la mierda que hice
- Familiarizarme con frames.js, ya que estaremos trabajando con esa baina
- Empezar a mirar sobre Index, para saber como funciona la data ahí

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

![[photo_2119476068_75.jpg]]

Okay new idea im making. Essentially Gamified Staking using our current pool contracts with some modifications.

The idea is fairly simple. Make a factory that deploys big bad Bosses, which are erc20 staking pool contracts, with set rewards, duration, and Health Points. 

Users then can do the following: 
-Join the fight by depositing, 
-Attack the boss by claiming
-Run Away from the fight by withdrawing. 

Attacking can be modified by owning one of our 404 Character Tokens. 

The goal is simple though, kill the boss before the Timeframe runs out. If you do, you get to claim your staked tokens, the staking rewards, and also, your portion of our 404 token that will be required to make a Boss. 
(could also potentially claim a poap, logic is tbd on that)

Why? To make staking more fun

Potential name is going to be UniBoss.

Alot of TODO's still but I think it could catch on...maybe.

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

![[photo_2119476068_75 - 20240416163117118.jpg]]

Okay new idea. Essentially Gamified Staking using our current pool contracts with some modifications.

The idea is fairly simple. Make a factory that deploys big bad Bosses, which are erc20 staking pool contracts, with set rewards, duration, and Health Points. 

Users then can do the following: 
-Join the fight by depositing, 
-Attack the boss by claiming
-Run Away from the fight by withdrawing. 

Attacking can be modified by owning one of our 404 Character Tokens. 

The goal is to kill the boss before the Timeframe runs out. If you do, you get to claim your staked tokens, the staking rewards, and also, your portion of our 404 token that will be required to make a Boss. 
(could also potentially claim a poap, logic is tbd on that)

If you lose, the boss wins, you can still claim your tokens, but the 404 token required to make the Boss would be sent back to the 404 treasury.

Why? To make staking more fun

Potential name is going to be UniBoss.

Alot of TODO's still but I think it could catch on...maybe.

***

**Forwarded from [Josh](https://t.me/JBate7)**

i have a call in 3 mins with 1KX. While im on it, please can you just braindump things that i need to know to actually pull this off live. Please help thank you

***

**Forwarded from [Josh](https://t.me/JBate7)**

it was fun, noone used the thing but the loved the PoK presentation

***

**Forwarded from [Josh](https://t.me/JBate7)**

The Embassy house wants to use the tool though i think

***

**Forwarded from [Josh](https://t.me/JBate7)**

i wonder if it would be possible to get a bot for Signal

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

Quasi-Clients
It's clear to me that the future of Farcaster depends on the rapid (but not too rapid) proliferation of alt clients to help people better consume the increasingly varied content casted on the Farcaster protocol.
It's also clear to me that alt Farcaster clients are not venture scale and, to be honest, I am probably going to continue to assume this until DMs are in the protocol AND I see a16z explicitly fund an alt Farcaster client.
But in our bubbling cauldron of transaction frames, cast actions, and content there IS monstrous opportunity.
It's just not in the straightforward "make a decentralized 'Insert Existing FAANG app here' with Farcaster" scenario that is easy to imagine.
I think the monstrous opportunity is in a class of apps previously unexplorable in the past 15 years of closed ecosystems that I call "Quasi-Clients"
Quasi-Clients won't actually be apps themselves, at least not at first.
They'll be neat code bundles that make your Warpcast experience a little bit better.
One will streamline your bookmarks or help organize your FC relationships, another will stack rank your casts to speed up your creation flow, and one will definitely let you drop poop emojis on your friends in a fun little external leaderboard that you only visit a few times a day but you can also view in a frame.
Quasi-Clients will gain DAUs because they're useful in the specific ways that Farcasters need them to be.
And seemingly overnight they'll be 10x more useful with only a button or 2 more than when you installed it.
Then, and only then, with 1K+ DAU users, will they ship a client.
Except when they do, it'll be to create something that CAN'T be done with Warpcast alone, all their users' data will carry over seamlessly, and everyone will simply log into it with Farcaster so they'll be running hot in <2 minutes

**Forwarded from [Josh](https://t.me/JBate7)**

#investment

***

**Forwarded from [Jade 0x](https://t.me/jade_0x)**

I would need to also understand more on how your token itself on “enforceability” and tokenomics work. Send whenever you have a chance!

***

**Forwarded from [Josh](https://t.me/JBate7)**

> we get the DeSci builders together
> onboard them into a TG groupchat with a trollbox attached to it 
> they introduce themselves and their projects and then forward it into the trollbox
> then, we discuss general topics of DeSci such as tools, limitatations, requirements, wishlists
> we continue like this and any notes that people take they forward periodically into the trollbox

***

**Forwarded from [Josh](https://t.me/JBate7)**

< the AI should be leading the conversation, giving tasks to people that are best suited to perform those tasks or most interested in those tasks

***

**Forwarded from [Josh](https://t.me/JBate7)**

**Profile**

Name:
What you do:
What you're most interested in / passionate about:
What you would like to know:

The trick is here, that if they dont share enough they wont get represented, if they overshare, they'll get over represented in the context

***

**Forwarded from [Josh](https://t.me/JBate7)**

Its easier to ask the AI to structure its response than to ask the people to structure their responses

***

**Forwarded from [Josh](https://t.me/JBate7)**

We proceed with the meat of the session with other preplanned topics. During this time we ask the participants to make notes, including what is being spoken about, what their thoughts are, what they want to see as next steps

***

**Forwarded from [Josh](https://t.me/JBate7)**

> lets create a shared objective here. Based on what you all like, e.g. how can we bring DeSci to more people. We can ask AI to suggest some common topics

***

**Forwarded from [Josh](https://t.me/JBate7)**

> then we can create a vote through the trollbox, and they decide which one of the options they prefer and why

***

**Forwarded from [Josh](https://t.me/JBate7)**

> then the AI can offer a suggestion for a research topic that we can tackle in order to progress towards that objective

***

**Forwarded from [Josh](https://t.me/JBate7)**

#dev #Solidant

***

**Forwarded from [Anže M. | Mickey](https://t.me/mickey_am)**

hey Josh, would you like me to back off on the topic of governance for a bit and return to this at a later stage?

***

**Forwarded from [Anže M. | Mickey](https://t.me/mickey_am)**

to let you guys debate and lock it down

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

cold dm'ed a gambling site (metawin.com) they're doing an airdrop for consistent users (token's MWIN) asked them if they wanted to contract out their staking pools or if they were going to offer them at all, and if so that we could do it fairly quickly.

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

hasn't launched yet but they directed me to open a ticket, so hope that iron in the fire plays

**Forwarded from [ivcained](https://t.me/ivcained)**

trollin

***

**Forwarded from [ivcained](https://t.me/ivcained)**

here

***

**Forwarded from [ivcained](https://t.me/ivcained)**

do you read everything i write here llm

***

**Forwarded from [ivcained](https://t.me/ivcained)**

you think you're better than me llm? You probably are SMH

***

**Forwarded from [ivcained](https://t.me/ivcained)**

I'm trying to add https://github.com/ivcained/basedhack.house/tree/main in Projects

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

### 🚀 Weekly Sync-Up Newsletter 🚀

Hello Team,

As we navigate another week of innovation and collaboration, here's a quick roundup of the key highlights and updates from our community. Let's dive into the latest developments and opportunities for engagement.

#### 🎲 Online Catan Sessions

Seth (aka Sparked) is rallying the troops for some online Catan sessions. With one more player already on board, we're looking for two more to join the fun. Mattim0use and Plat0x_eth, you're being eyed for the spots! Interested parties, let's get the dice rolling.

#### 📚 Nerdery Channel Update

Plat0x.eth shared a link to the Nerdery channel on Warpcast. It's a space for all things geeky, so make sure to check it out for the latest discussions and resources.

#### 🌐 Focus on Arbitrum

RodoTritön emphasizes the importance of focusing on Arbitrum, sharing a link to a discussion on thriving with Gitcoin. It's a call to action for those interested in contributing to the ecosystem's growth.

#### 📝 Grant Template for Obsidian

A comprehensive grant template for Obsidian has been shared, covering everything from problem description to financial plans and risks. This template is a valuable resource for anyone looking to structure their project proposals effectively.

#### 💰 Funding Rounds and Grants

Details on various funding rounds and grants have been disclosed, with significant allocations for Open Source Software, web3 infrastructure, dApps, and more on Arbitrum. Keep an eye out for upcoming opportunities with ENS, Hypercerts, Open Civics, and TEC on Optimism.

#### 🛠 Tech Insights

Raquel Raigal has shared insights into the Gitcoin grants stack and raised questions about funding pools on Polygon. For those interested in the technical side of things, this is your call to action to dive deeper into the mechanics of funding and allocations.

#### 🧠 R&D and Development Updates

A summary of recent challenges and achievements in R&D, digital data analysis, and development work has been shared. The use of haiku as a medium for encoding and decoding updates is highlighted, emphasizing innovation, problem-solving, and the strategic use of language.

#### 🖼 Frame Voting for Hackathons

ivcained is exploring frame voting for hackathon submissions and seeks insights for integration with the mud-hackathon platform. This initiative opens up new avenues for community engagement and support for hackathon participants.

---

As we continue to push the boundaries of what's possible, your contributions and insights are invaluable to our collective success. Let's keep the momentum going and make this another productive week!

Stay innovative,
[Your Newsletter Team]

***

**Forwarded from [Plat0x.eth | Σ🧠Ξ I WILL NEVER DM YOU FOR MONEY](https://t.me/Plat0x_eth)**

### Internal Newsletter: Week 001 Update

---

#### **Highlights of the Week: Proof of Knowledge (PoK) and DeSci World Initiatives**

This week has been bustling with activities, focusing on the advancement of Proof of Knowledge (PoK) and the development of the decentralized science world (DeSci World). Here's a comprehensive summary of our key activities, discussions, and strategic moves.

---

### **Proof of Knowledge (PoK) Developments**

- **Zettlekasten Method Integration:** Eneko Uruñuela has proposed integrating the Zettlekasten method into our PoK system to enhance performance. This method, known for improving knowledge management and synthesis, could significantly benefit our AI's ability to generate more accurate and contextually relevant responses by prioritizing synthesized knowledge over entire documents.

- **Hallucination Testing:** Eneko has also initiated research on hallucination testing across different models. This effort aims to refine our AI's accuracy, ensuring it provides reliable and verifiable information, a crucial aspect of our PoK framework.

- **Obsidian for Organization:** A workshop led by At0x discussed the potential of using Obsidian for internal organization. The team deliberated on the advantages and challenges of this approach, agreeing to create a daily report channel to streamline communication and project tracking.

---

### **DeSci World Initiatives**

- **Grant Template Creation:** Rodo Tritön has sketched a comprehensive grant template for Obsidian, outlining essential components like problem description, solution proposal, and impact assessment. This template will streamline our grant application process, ensuring clarity and consistency in our proposals.

- **DeSci World and CICM Collaboration:** Luis Maumejean presented a proposal for collaboration between DeSci World and CICM to create the DAO (RC-CICM). This initiative aims to position CICM at the forefront of technological innovation, leveraging DeSci principles to foster knowledge generation and community benefits.

- **LENR DAO Developments:** Diadon has been identified as the lead for the LENR DAO, focusing on building a structure to support Low Energy Nuclear Reactions (LENR) research. Despite facing challenges in rallying initial interest within the www.ISCMNS.org community, Diadon is committed to spearheading this transformative project.

- **Governance and Funding Strategies:** Discussions around governance models and funding strategies have been pivotal. Anže M. | Mickey has explored the use of MakerDAO's endgame smart contracts for subDAOs, emphasizing the need for a robust governance framework to support our initiatives.

---

### **Operational Updates**

- **Obsidian Integration:** The team is exploring the use of Obsidian to enhance our internal knowledge management system. This tool's potential to return references and organize information efficiently could significantly benefit our project coordination efforts.

- **Community Engagement:** Josh has emphasized the importance of forwarding relevant information to the BRAINZZZ channel. This initiative aims to enrich our data vault with diverse inputs, enhancing the AI's learning and our project's overall development.

---

### **Looking Ahead**

As we move forward, our focus remains on refining our PoK framework, expanding the DeSci World ecosystem, and ensuring our projects align with our mission to revolutionize the scientific research landscape. The integration of advanced methodologies, strategic collaborations, and a commitment to open knowledge and innovation underpins our efforts to achieve these goals.

Stay tuned for more updates as we continue to push the boundaries of decentralized science and knowledge sharing.

---

**End of Week 001 Report**

*For any inquiries or further information, please contact the project leads or refer to our internal communication channels.*

***

**Forwarded from [Raquel Raigal](https://t.me/rraigal)**

#bonsai test for the backend

***

**Forwarded from [Raquel Raigal](https://t.me/rraigal)**

#PEX plataforma para intercambiar puntos provinientes de recompensar por poseer memecoins

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

https://github.com/PopPunkLLC/gaslite-core/blob/main/src/KingOfTheFrame.sol

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

I wanna check this out at some point: https://github.com/PopPunkLLC/gaslite-core/blob/main/src/KingOfTheFrame.sol

***

**Forwarded from [Seth (aka Sparked)](https://t.me/Sparked0x)**

https://github.com/0xsks/crypto-horde-scaffold

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

Yaaas okay

***

**Forwarded from [mattimouse](https://t.me/mattim0use)**

Gonna stick figure this app together haha