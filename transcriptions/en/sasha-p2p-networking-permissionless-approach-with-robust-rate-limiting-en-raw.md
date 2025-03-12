# Sasha

_**P2P Networking: Permissionless Approach with Robust Rate Limiting**_

_This presentation explores innovative strategies for developing permissionless peer-to-peer networks that prioritize privacy. It delves into mechanisms for preserving user anonymity while implementing effective rate limiting solutions to ensure network efficiency and security._

[https://youtu.be/OhK6DcB7pCc](https://youtu.be/OhK6DcB7pCc)

---

_**Sasha:**_ Hello, my name is Sasha. I'm from Waku protocol, and today I'm gonna share some ideas what we developed within Waku for rate limiting peer-to-peer networks.

I propose to go through some recap. So I'll go through some current state of peer-to-peer networks, some properties we are looking at mostly. Then two network types that we have right now, meaning that been parallel network types opposite to each other. Then trust assumptions in which these network types oftenly operate. Then common struggles that peer-to-peer networks do face every day. Then we will go through rate limiting in first type and the second type, and then how Waku does it differently. And then we will go through Waku network, some protocols we implemented, the history of it, and why do we even care building it, actually.

So, recap. We look for some properties of the peer-to-peer networks and some of them are that we don't have a central entity that govern this huge, let's say, network. This is what we ideally want. This is not essentially the case because it depends on architecture, and it might be the case in terms of, let's say, hardware or logical… but not hardware, or only hardware but not logical point of view. I'm sorry.

Then what other properties we're looking at is direct data and computation sharing. So let's say me as a peer, I can ask someone else to allow me to use CPU, GPU, or data storage, or ask some questions, just a communication as it is done within human beings.

So ideally we don't want a single point of failure and this goes from the first property, like there is no vivid centralized entity.

Then redundancy. In the ideal world we want peers being interchangeable so that when we have some problem with one person, we can just discard it and start all over with another one. This is what we want to have.

And privacy. This is the most important point, honestly. But it goes from the way that, since we are in charge of what we are given to the network, then we can assume that we are private, because we are not leaking anything extra that we don't want to leak. Essentially, reaching the privacy here.

So network types, two opposite ones. Centralized and decentralized. Maybe. So why maybe? Because it can be implemented in one way or another. It can be decentralized in real world, there are many computers, but still it is centralized, meaning that there is like a spread central entity that is kind of governing it. Or it can be just one computer and claiming to be decentralized. Yeah, we know it. There are many cases of it.

So trust assumptions for centralized networks. So we do just trust blindly, actually. It's nothing more than that. We do believe that whoever provides us this, let's say, HTTP server can be trusted. The software that they run is good, it's not malware. Then another meaning of achieving it is to have SSL certificates and domain names. So let's say if I go to facebook.com, it is a Facebook, and I can check the certificate. And again, it goes to some trust, to some authority that signs this certificate. So still, we are hitting this someone here.

And authentication tokens. So authentication or authorization tokens are widely used so that we can authenticate some user to our server, if we are a server, or we have some identity on this particular server. So this is main things and they done for different purposes. So some of them are for the DDoS protection, some of them are for just how it is done because it cannot be differently done in that environment.

Trust assumptions for decentralized networks — usually they are open source. Of course, there is an asterisk, because some of them are not. And let's assume that they are open source and we can read the code.

Then signatures are widely used so that when I'm talking over this network, I can understand who am I talking to and I can validate it easily without a problem. So using a clear mask that everyone of us is knowing.
And transparency, because the code is open, it's probably signed by a developer. Also, when we talk through the network, we understand who we are talking to. This is more or less what we expect to get from there. It's not a black box for us. It's something open. We can go there and refer to it anytime we want to.
And what common struggles are there for the peer-to-peer network? So quality of service is the obvious one. Like if we expect this network to provide us, let's say, I don't know what it is, a network for having the meetings, right, direct talk to each other. This is quite easy to do because you just give your peers a direct way to talk to each other and that's done. But if you need to do something more sophisticated, like let's say, many to many connection, right? It goes hard, it's difficult to implement and difficult to do it clearly without referring to again, central entities and something. So quality of services.

Scalability, again, the same point. It's very hard to scale it up to let's say, million users, 10 millions, one billion users. This is what we essentially want, but the mass is not there, the search is not there yet, and we are slowly getting there.

And the latency as well, so because we don't have much control over the peers, the bandwidths, the network interfaces, whatever, it can be sluggish, so yeah, this is what we face with.

Connection management is a big сon as well, because someone may be behind the net, and we just cannot dial it, we just cannot connect to each other, so done nothing here.

And the most difficult one, and this is the topic of today's talk, is spam and the DDoS protection — how do we reach it in a way that we don't have someone telling us: "Yeah, you can receive messages or data from this person, from this IP, and do not receive from this IP". How do we do in a decentralized manner so that there is no one governing it like on some single point of failure?

So how it is done in centralized? In centralized way, it's done with access tokens generated for API. So just easy as this. You have a capacity, here is your token. Refer to me with this token. And then later we can renew it. That's it. For users, it's a bit different. There is authorization or authentication token. And essentially, the idea is the same. You just authorize, you get the token, and then you refer back to me with this particular string. And if it is valid, good for you. If it is not, goodbye.

And IP reputation. This is something which is more done on an infrastructural layer where, let's say, the IPs of, let's say, botnets will just see the misbehavior in a pattern and just slash it. We just stop receiving it. It's very easily done because there is just a mask applied. So no computation needed that much.

What is done right now in decentralized networks is peer scoring. This can be perceived as DDoS protection meaning that if we see some patterns from peers that they misbehave in a way, let's say, I provide some set of protocols, set of resources to you, to a network, and I see you are abusing it in some way. We can implement some other protocols on top that will be tracking it, and it will be just discarding the peers that misbehave. Again, this is still very difficult and challenging to rely on, because peers might just create a new identity. I don't know, if it is a wallet used, or a wallet address, or some mathematics around it, then just a new one. So a new peer ID, then yeah, that's it. So spam all over again.

Token-based approach is also used. This is very easy to implement on a level of a peer, or a level of a network. On a level of a network, it's more harder, because then you have to refer to some distributed data storage, so that you can see that the token is still valid and capacity so there is a lot of updates, create, retry, delete at some point. So this is what we don't want to do because if the network goes up and up, like more peers, more nodes in the network done, we are busted with this one, we cannot do it.

So our proposition to it is rate limit in nullifiers. So this is what we researched within Waku and Vac, which is a research group that we have. together with privacy and scalability organization. So in a nutshell, it works with the ZK magic. So we will refer to that the ZK magic is trustful. And these are the steps that the rate limit and nullifiers do.

So first step is that if you want to participate in the distributed network, you need to create some kind of identity. It's not like identities that can identify you, but you need to create a secret under some, in this case a contract, yeah, I know, EVM compatible, as of now. But we can do something later on with this. So some secret should be created there and stake being done so that there is money on cone so you're not misbehaving, there is some things that you care about.

Then this secret is used to create proofs per data packets. So let's say you send like 10 data packets per second, you need to create a proof for each and every one, for each chunk of this stream of data that you are doing. And then what is embedded into this ZK circuit — is only some specific amount of messages, data packets, whatever chunks of data, read it as you want, only specific amount of them can have proofs generated. So let's say per second, per one epoch, and in our case, this is a second as of now. Per second, you can generate only five messages. Any peer in the network can generate only five data packets. For Waku it's 150 kilobytes, so it's kinda okay. So every second, five packets, let's say. And if you generate a sixth one, then at this point anyone who received your data packet, and for the Waku network this is the case, everyone receives your data packet under specific topic, which I will go into detail later on, can derive your secret from there by using the same ZK circuit. And once the secret is derived, you can, whoever derived it, can go to the contract and just take money. That's it, as easy as it is.

And after that, all following proofs are invalid, because this person, this secret, is not anymore a part of this anonymous pool, so that there is nothing that makes these proofs valid. Anyone who will receive them following and will be revalidating them against this table of whatever secrets, of course, secrets are not visible, will be invalid.

So yeah, this is the proposition. But we are not done with the presentation yet. So I mentioned Waku a couple of times. This is what I am building these days. And the Waku network, we aim to have this as a communication layer for any web3 applications. This is something that operates already. So there are like 500 nodes or even more. The stats are open. If you operate your own node, you will see the network so that you will see however much data is exchanged or how many nodes are there. And we provide the permissionless communication layer so that anything of your application, any one of your application, or infrastructure application as well, it's not only about the apps, can use it for exchanging data between points A and point B.
So, and history of it started in 2013 when Ethereum came out. And there were like three pillars at Ethereum. Everyone probably knows it. And there was Ethereum for consensus, there was Swarm for data, and there was Whisper for communication. And Whisper was quite okay at the time, there was nothing better. And until 2019, there was not much of development. It was kind of good, it was kind of used by some projects, some protocols, it's okay, it's fine.

And at 2020, Status came up, and Status is a messaging application, and they understood that there is a need for continuation of such a protocol because it should be done in a better way. There was such simplistic heuristics being made in the first iteration that we just had to revisit it again, which we did.
And we came up with the first implementation of Waku and by re-implementing it by using libp2p as a technological stack for us and some doing settings on top and other things. We reached improved bandwidth, so our devices were not heating a lot. You didn't spend gigabytes of data per second on just relaying everything for the network. So this was good, but not ideal.

And we continued improving upon it, and we implemented it in three other languages, so JavaScript, Nim, and Go. And they are referencing implementations. Nim language implementation is the best one to refer to. So if you want to check out. And up to date, we are continuing building it, and we aim to have it as the default standard because everything that we develop is open, every protocol is open, and we try to standardize them for many other applications such as Internet of Things, Wallet-to-wallet connection and other stuff.

And short overview of protocols. Any peer-to-peer network starts with a discovery. You just cannot bypass this step. You either already know someone you want to talk to, like a network or a website, right? Or you need some help with discovering that. So usually it's done by using bootstrap nodes. And we do use this one. We do have DNS Discovery implemented. This is one of the discover protocols we do have.

Once you get started, it's fairly easy to continue. And we implemented some other protocols on top. So we do have peer exchange. And once you are part of the network, you can start talking to a network and ask it: "Do you know more peers? If you know, please share an address with me". And then you can start establishing this connection or keeping it for later use. Maybe this peer is a long-lived node, you can use tomorrow, a month later on, and so on and so on.

And then there is another network on top of Waku, which is a fork of Ethereum's Node Discovery V5, which is Waku V5 right now, Waku Discover V5 protocol it's called. We did some changes there, and this is a parallel network that is used together with Waku that gives a lot more stability for bypassing stamp steps during discover and making it more robust.

Yeah, so, relay. Waku operates over relay protocol, which is a libp2p GossipSub protocol, if you know. If you don't, this is essentially a way for a specification that says under one given topic, if you receive a message, just relay this message to other peers that you didn't receive this message from. And it works, but you understand it's not ideal, just it's stupid, you cannot scale it, right?

So there was many iterations for the GossipSub, and right now we do have GossipSub V2, I think. This is quite good one and we continue to prove upon it and trying to figure out good pros and cons, good tradeoff for Waku in particular.

So we do sharding on top of GossipSub, so there is not like one big huge topic under which all network operates. There are some sub shards of it and this way we reached according to our emulations, scalability up to one million users or even more. So let's see how it goes. This employs Pub/Sub architecture, so you just subscribe and you just receive the messages from the network.

RLN I already mentioned, this is something that was done together with the privacy and scalability organization. And we are continuing to improve upon it. Right now there is Ethereum EVM compatible contract existing and it can be deployed on any EVM compatible chain. Right now this is deployed for this on Sepolia and we are working on maturing this primitive so that it can be used interchangeably and even separate from Waku. It's not something that we own. Everything is open, code is open. Just take it and use it in your project. There are many more applications to this algorithm than just peer-to-peer networking as well. It can be done on the application layer as well and used very well.

Yeah. And light protocols. So we always keep in mind the light clients, such as phones, browser tabs, et cetera, et cetera. This is something that we implemented specifically for them because GossipSub is very tough to run on a browser, in the mobile or something. So you just use request-response. Everyone knows this pattern of development. It's fine, it works.

And the properties that we reach by having these protocols in mind. We are private by default. Everything that I was leading to is that everything is embedded, this privacy is embedded on every piece of code that we write. And this can be validated against the specifications we develop and review. This is permissionless, anyone can join the network, anyone can participate in the network and influence it on the level of discovery, on the level of providing services, as well as just using it for your own and not providing anything on top that you want to provide. This is security scalable because of sharding, because of RLN, the scales, and it does work. Of course, we still have a lot of more bottles to test it in, so please help us with this one. And this is very friendly for Litecoin because we always keep it in mind and we develop particular protocols for it.

Yeah, and why do we even bother? So this QR code leads for our principles and values as for research and as for development. And we do believe in sovereignty of individual and we think that it can be reached only by embedding transparency in every piece of code that we write, every software we develop, every research we do.

And yeah, that's it, thank you.
