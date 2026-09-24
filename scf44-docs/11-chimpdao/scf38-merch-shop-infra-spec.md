Source: https://docs.google.com/document/d/1y8tlgZi5UyObRoe0pXJfjQWrKpAOz19NLrQar7pi7sU/edit?usp=sharing

﻿Stellar Merch Shop
Infrastructure specification
Intro


The following document describes the current infrastructure we have in mind. Things will change as we implement the solution and gather awesome feedback from the community.


After an overview and a high-level explanation of the flow, we will present individual components in detail. At the end, we have put ideas for future developments, a sort of brain snapshot of the team if you will.
Overview


  



💡Maintainer: a person which is part of a project’s team.


1. Using the dApp, a maintainer registers a project providing a unique name, some metadata and a list of maintainers. Projects are typically awarded SCF projects;
2. The project’s registration, as well as main operations, triggers an event which can be listened by anyone on the network;
3. Either using the dApp, or from tools provided to directly call the contract, maintainers can update projects;
4. Maintainers can create series and creators can propose some new designs using the dApp. Creators get royalties from their design as part of the NFTs lifecycle.
5. Users can use the dApp to easily see all projects, participate in the DAO and place orders. These orders end up triggering on-chain operations to mint related items.
6. NFTs can be exchanged, generating royalties for creators and projects. They can be used to join specific communities or participate in events. No more Luma code needed.
Open-Source


Our codebase will not only be open-sourced, our development will also be open on GitHub:


(link to follow one week after the project is approved and starts)


The project will be tracked using a GitHub Project board. We will fill the board as soon as the project is accepted since this funding is essential for the completion of the tasks.


We will ensure that we provide comprehensive documentation and that the code can be easily understood and audited. Our partners will need guarantees that we are only doing what we are supposed to.


Regardless of a hypothetical monetization, we have a strong commitment toward open-source and our code will remain public.


Soroban Contract


The Soroban contract handles all on-chain features of the projects from registration, administration, to creating and minting NFTs.


When maintainers register their projects, a unique hash based on the project name is created. This serves as a project_key to uniquely identify a project and is stored as a data entry on the ledger. We will use Soroban domain as a way to prevent name squatting and other nefarious registration. The community will have easy tools to report issues. This will effectively prevent misuse of our contract.


To provide the project, membership and DAO features we will be integrating with the Tansu project (from the team.) While Tansu is originally thought for coding projects, a lot of components can be adapted so that the platform is generic. It already provides project segregation, social trust, domain protection, IPFS linking logic, etc. We will only rely on the smart contract components of Tansu.
On the NFT side, we are planning to leverage SEP39 and OpenZeppelin contracts OpenZeppelin-stellar-contracts. This would provide a secure base for our implementation. There will be a concept of ownership and royalties (mainly to pay creators.)


NFC to NFT
After discussing with current partners, we will make samples of garments which are uniquely identifiable. During the discovery phase, we will have a look at various things ranging from simple QR codes to NFC garment tags. E.g. https://threadfast.com/pages/180nfc-the-tap-tee#shopify-section-template--17421770948799__video_4ityAF and https://seritag.com/nfc-tags/garment.


Once we settle on the approach, we will have to link unique items to specific NFT in their respective series. This is a challenging supply chain problem. Depending on our suppliers, we might not be able to do the linking during the manufacturing process and would either need to have an extra process before final shipping, which would be hard for scaling, or rely on customers to register their goods upon reception. We are not scratching any ideas for now and will brainstorm with our partners.
dApp


The first version of the dApp will be simple and focus on a basic feature set. We first need to build trust with the community and only then we can expand our feature set with what our users actually need.


The dApp will leverage well-known JS SDKs to propose an effective and familiar user interface. We will start with stellar-sdk, sorobandomains-sdk, and scaffold-stellar. This will help us build faster and handle addresses and signing safely so that our system never sees a user's secret key–hence fully non-custodial. On that note, having a list of maintainers allows a project to have “social backups” in the sense that another maintainer can help recover access.


Maintainers will be able to handle all aspects of their projects on the dApp. They will have tools to add or remove maintainers, update metadata, create new series, etc.


In general, the interface should be clear enough to show where the data is coming from and there should be a simple way to see the data on-chain and off-chain.


Shopify Frontend & Customization


The Stellar Merch Shop frontend will be built on Shopify to leverage its proven e-commerce infrastructure, global payment gateways, and mature logistics ecosystem, ensuring reliable order processing, inventory management, and international shipping from day one.


However, rather than relying on limited standard Shopify themes, we will fully customize the storefront using Liquid, Shopify’s templating language, in combination with JavaScript, HTML, and CSS. This approach allows us to:
* Implement a unique, Stellar-branded UX/UI aligned with the project’s visual identity.
* Integrate blockchain-specific features directly into the product pages and checkout flow, including NFT minting triggers, royalty tracking, and proof-of-authenticity verification.
* Embed dynamic content such as NFT ownership status, DAO voting links, and collection management dashboards without breaking Shopify’s native performance optimizations.

Logistics & Backend Integration

While Shopify will manage traditional e-commerce operations (SKUs, stock, shipping rates, tax handling), our custom Liquid templates will connect to the Soroban smart contracts and the dApp backend via secure API calls. This will ensure that:
   * When a product is purchased, the corresponding NFT is minted and linked to the unique physical item (QR code or NFC tag).
   * NFT metadata and ownership records are displayed in the customer’s account section.
   * DAO and community engagement features are accessible directly from the store interface.

By combining Shopify’s robust commerce capabilities with deep Liquid customization and blockchain integration, the Stellar Merch Shop will deliver a seamless Web2-to-Web3 retail experience — intuitive for everyday users, yet fully powered by Stellar’s on-chain infrastructure.


________________




Ideas for future developments


This list is also present in our general timeline.


Upon a successful activation phase, and provided we receive further funding, we will be able to add more features towards project management and around the NFT lifecycle. The following list is subject to changes as we implement the project, uncover new use-cases, and most importantly, get feedback from the community.


      * Get on-chain through Merch. A piece of merch could be attached to a wallet and incentivize people to try out Stellar. We could pre-load a wallet with specific tokens or positions based on the type of event.
      * While we will start with a selected number of projects, our goal is to provide a real launchpad for SCF awarded projects. We will have to have strict guidelines to ensure the best quality of service. One idea would be to connect to the SCF funded database to verify projects at registration. Then we can have these projects being proposed to the community via the DAO. Not all projects will need nor want to have merch. This is ok and this has to be an open discussion.
      * Merch is a way for projects to diversify their revenues or help them quickstart their journey. Maintainers could decide to get part of their sales sent to a project wallet. The DAO could be used to control part of the funds allocation. Funds could be used to pay creators, improve quality of merch while keeping prices low, fund the project development itself, etc.
      * Other personas will be added on-chain as to give other responsibilities. This will further increase projects' transparency and give all participants some visibility.
      * Support multiple technologies for the NFT-Merch link. Depending on the merch, we might want to use different form factors.
      * Be able to subscribe to projects to get news about drops, events, DAO proposals.