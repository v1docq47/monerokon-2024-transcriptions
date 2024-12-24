# Aaron Feickert

_**How Do We Design Secure Protocols?**_

_When we look at cryptographic protocols like CLSAG and Bulletproofs and Triptych and Seraphis, we often talk about things like security models and proofs. But what does this mean?_

_In this talk, we'll take a brief look at what it means to build a secure cryptographic protocol and analyze it. Taking a Monero-centric approach, we'll introduce the idea of a security model and use examples from the Monero ecosystem to show why it's an important part of analyzing these constructions._

_This talk will be accessible to everyone, and will provide a broad look at what cryptographers do behind the scenes to keep Monero safe and secure._

[https://youtu.be/v6Yf9u95HW0](https://youtu.be/v6Yf9u95HW0)

---

_**Aaron:**_ So my talk will contain no math, so don't worry about that. And we're right before lunch, so the best kind of talks are short and sweet, and the second best are short. So, try to make this short and sweet.

So we're gonna talk a bit about… oh, this is not financial or whatever advice, whatever. So when we look at cryptographic constructions, and there are many, we have signature schemes like CLSAG and Monero, or Bulletproofs, Bulletproofs+, Bulletproofs+++ that we use for things like range proving or even things like transaction protocols like Seraphis from Monero, or Spark is a similar one that I worked on, we often talk about security and proving things secure. But we don't often talk about what that means. So we're gonna very briefly go over some techniques that we can use to show that something is secure cryptographically.

So there's many different ways to formalize a cryptographic construction in a way that allows us to analyze its security and then to prove it secure. So we're gonna show some strategies that are used here. By no means is this comprehensive. There are many different ways, frameworks, methodologies to prove security. We're gonna talk about kind of one specific area of it. We're gonna do this using examples, specifically from Monero. So if you're already familiar with this at a technical level, you'll say: "Ah, this isn't comprehensive." No, it's not intended to be. Just an overview.

So let's give an example, which is a digital signature. A signature scheme is a cryptographic construction that's used to authenticate messages. These are used very frequently. Monero uses them, Bitcoin uses them. You use them all the time every time you use your phone to authenticate messages using keys. And the basic idea is that if I have a signing key and a message that I want to sign, I, the signer, can produce cryptographically a signature. And then you, who want to verify the signature, have a corresponding verifying key. You take the verifying key, the message and the signature that I produced, and you determine if that is an authentic or valid signature or not.

And so to formally analyze the security of any signature scheme, we have to formalize and break it up into different algorithms. And cryptographically, the way we usually do this for the signature example is we'll take kind of three different algorithms. The first of which is to generate the signing and verifying key in a key generation algorithm. What these algorithms look like depends specifically on what signature construction you're looking at. Then we have a signing algorithm, which I use to take a signing key and the message that I want to sign, and it spits out a signature, using whatever kind of math or cryptography the different schemes specify. And there's a corresponding verify algorithm that takes my verifying key, takes the message, and it takes the signature, and it spits out a "yes" or a "no" if that signature is valid on that message for that key or not.

Now, to take these algorithms that we formalized in a given signature scheme and prove security, we need to formalize what's called a "security model". And in this case, the security model basically says what properties do I want this to have, and how can I formalize them in terms of so-called "security games" that we play against an adversary who wants to break the properties of my scheme. So each property that we decide to formulate in the security model, has a game that we have to define that we play against an adversary. If the adversary can win the game that we specify, then it's broken this property, which is bad. We don't want that. And then we use a security proof to show that there is no such adversary that can win the game, and then our scheme is secure. So when we talk about security, we have to talk about it in terms of the security model that we define.

So one property that we typically want signatures to have is unforgeability. And this kind of makes intuitive sense if you think about it. I don't want signatures to be forged without the signing key. Someone who does not have the signing key should not be able to produce a signature that you can convince someone else is real. So we'll define a game about forgery. So we play this forgery game against this hypothetical cryptographic adversary, and the adversary's goal is gonna be to give us a valid signature on some message that we think is legitimate when it doesn't know the signing key. He shouldn't be able to do that, it's a forgery. If the adversary can't win the game, great. The signature design is what's called unforgeable. So this is part of our security model and there's other properties that you might want too.

So how do we define this game? So the game is played between us and the adversary, and it has different steps. The first step is that we generate a signing and a verifying key, but we only give the adversary the verifying key. If it had the signing key, then, well, of course you can forge a message if you have the signing key. That's how it works. The adversary then will hand us a message and it will say: "You, please, sign the message." We who have the signing key will go ahead and sign the message and hand the adversary the signature. And the adversary is able to do this effectively as many times as it wants. It can keep handing us messages, we'll keep signing them and handing them back. And then at the end of the game, the adversary needs to give us a new message that it has not sent to us before, and a claimed signature. And the adversary wins the game if the signature is valid. So all it has is the verifying key and a bunch of signatures that it asked us to produce, and then it has to send us a new signature that tries to produce via forgery.

So notice something about this game. We gave the adversary some powers. The first power is that it did get the verifying key, which makes sense. In the real world, say in a blockchain type situation, in order to verify transactions, everyone, the entire network, the entire world, needs access to these verifying keys. So it makes sense to give the adversary the verifying key. The adversary also had the separate power where it could ask us to sign any message that it chose. And kind of in a blockchain signature context, this may be equivalent to asking someone to just sign a message for any purpose. It could be for some kind of like login or authentication scheme. It could be that the adversary tricks us into sending a transaction to someone of its choice.

The idea is that the powers that the adversary has in the game that you define should reflect the real world as much as possible, because the security model only makes sense in the real world if you've modeled the real world correctly. So the idea is that you want to give the adversary as many real world powers as possible, which we did in this game.

And now we need to actually do a security proof. So the property of unforgeability that we talked about is very general. And all you have to do is have an idea of signing and verifying. And there's lots of different ways to do this. For example, ECDSA signatures are one that's used, for example, on Bitcoin. Schnorr signatures - there's a variant of those that's used in Monero. You can do RSA type signatures - all sorts of things. But while that property and the game are general to the idea of signature schemes, the security proof that you have to do in order to show that there can be no adversary that can win this game is very specific to whichever security design you choose, whether it be ECDSA, Schnorr, something else. And the idea is that will show that if an adversary could win this unforgeability game, where it hands us the signature that it forged, then we could turn around and use that to solve some other problem cryptographically that's believed to be hard. So there's always these ideas of kind of hard problems in cryptography, and we want to show that we can kind of reduce our security game down to a known hard problem.

So the idea is that we want to sort of trick the adversary that we've kind of constructed in our heads into solving this hard problem for us by winning our game. So one common signature scheme called Schnorr signatures. For those, the hard problem is a cryptographic problem called the "discrete logarithm problem". And the idea behind that is basically the idea that you can reverse keys by taking verifying keys and turn them back into signing keys. If this problem were broken, which it's believed over decades of research not to be breakable, it would horrifyingly break the internet and the world would start on fire and stuff.

So the hard problem that we're gonna look at is called the "discrete log problem" and it has its own game. The idea behind the discrete log problem is that we should be able to effectively produce a so-called "discrete logarithm". So if you go and actually prove Schnorr signatures, in this case secure, the proof of unforgeability shows that we can actually win this unwinnable discrete logarithm game by passing information along between our forgery game adversary and kind of the universe. And the idea is that if the adversary were able to win this unforgeability game, we could use that to solve an unsolvable problem. Well, that's a mathematical contradiction. And if that's the case, then that means that there can be no such adversary. And the proof formally shows that this is the case by very carefully formalizing the way that these different security games interact with each other.

And it's worth knowing that we don't actually know for sure that hard problems like the discrete logarithm problem are in fact infeasible to solve. It's just decades of research has shown this to be the case. So part of formulating a security model is determining what problems you believe to be effectively unsolvable. Discrete log is one of them. It was mentioned for example, back in the Bulletproofs++ talk.

So a Monero example is CLSAG. So Monero currently uses a different variation of a signature, called a linkable ring signature, to authorize transactions. So we often talk about Monero having ring signatures. This is the ring signature that Monero uses. Effectively, you're signing a message on behalf of a possible set of signers. It also has an unforgeability property that's kinda sorta related to the one for standard signature schemes, but a little bit modified. And its property, this unforgeability property, has a game that requires the adversary to produce a signature using a list of possible keys. It's a similar idea. The adversary wins this game if it's able to provide you a fresh signature where it doesn't know specifically different parts of the signing keys.

And when we originally did the proof for CLSAG, I worked on it with some colleagues, the game actually required that the list of keys that the adversary used for its signature had to be honestly generated. But that's not necessarily how the real world works. In the context of say Monero, you don't have to go to some authority and say: "Hey, give me a signing and verifying key to make transactions." No, you generate those yourself. And if you're an adversary, you might try to generate those keys maliciously.

So initially, the entire security model, like many of them before that, were premised on the idea that keys had to be generated honestly. And fortunately, auditors pointed out that this is probably not ideal. And they recommended that we change the game to let the adversary throw whatever keys it wanted into the construction, which gives the adversary more realistic powers. And then we were able to modify the security proof to account for this. So a cool example in the real world of how different powers given to the adversary can affect the security model.

So let's look at another example, which is range proofs, which were spoken about earlier with Bulletproofs++. Part of showing that a transaction is balanced and that you're not effectively like creating money out of thin air, requires that you use what's called a range proof, as was discussed before. And the idea is that a range proof asserts that the amounts that you're hiding inside different transaction elements represent a hidden but valid amount, that's not negative. So in Monero, the way we do it is we take amounts and we use a value for the amount, and we use a random looking so-called "mask", and we throw those together and form this cryptographic blob called a "commitment" that hides the amount, but in a very structured way.

And the idea is that we want to represent this so-called range proof using what's called a "proving relation". And a proving relation is a very general term that shows how different public and secret data in a proving system interact with each other and relate. So in the example of a range proof, which uses these commitments, the public data that everyone gets to see as part of the transaction is this commitment, this cryptographic blob that represents the amount in a hidden and structured way. And the secret data is the value that was represented there and this random-looking mask that you use to generate the blob. So the relationship between the secret and the public data for range-proof commitments is first, that the commitment was produced using that value and that mask, which are secret, and second is that the value that's secretly represented there is within a valid range that's not negative, which we can't have.

And the security properties that we use for proving systems, like Bulletproofs, Bulletproofs+, Bulletproofs++++, is that the prover uses the secret data to build its proof, and then a verifier, who's anyone on the network, anyone in the world, uses only the public data to verify the proof. It doesn't know the secret data, it's secret.

And there's several security properties that you have for something like a range proof. One of them is called "completeness", which says that if the prover actually knows the secret data used to build a range proof and a commitment, then the verifier will accurately judge it as being correct. So honest proofs are honest and valid. The second is "soundness", which Liam hinted at in his talk as well, which says that if the prover doesn't know the secret data, in this case, the value and the mask used to build the commitment, then the verifier will always reject the proof. Effectively, you can't fake a proof if you don't know the secret data. And the third property that you often want is what's called "zero knowledge", and informally, that means that the verifier can't learn the secret data by looking at the proof. And there's very technical definitions of this we won't have to go into today.

But a common way to show that soundness holds, that is that the prover knows the secret data and can't fake the proof, is to build something algorithmically called an "extractor". And an extractor is an algorithm that basically pretends to be the verifier, but algorithmically and technically asks the verifier to generate a bunch of different proofs for the same data in a very specific technical way. And then the extractor, this algorithm, shows that if it did have access to a bunch of these proofs, it could go and basically pull the secret data out. And you can use that approach to argue that if this extractor can pull the secret data out of a bunch of proofs in a very structured way, then the prover must have known that to begin with. The very common proof technique. So take a bunch of proofs, show that you can yonk the secrets out.

And Monero has actually used three different range proof designs throughout its history. The first one used a variation of ring signatures called Boromean ring signatures. The second was Bulletproofs which is a more efficient range proving system. And the third and current one is Bulletproofs+, which is a differently structured and more efficient version of Bulletproofs. Each of these have the same three properties - completeness, soundness, and zero knowledge, but each of them is proved in different ways.

So the security properties, kind of like the ones we talked about for signatures, they apply very generally to a bunch of different proving systems, not just to range proofs. All you basically need is a proving relation that has some kind of public data, some kind of secret data, and some link between them.

So why do we like these particular properties, like completeness, soundness, and zero knowledge? Well, why we like them is we can basically stack them up in order to build much more complex systems. So a range-proving system, for example, is one part of the Monero transaction protocol, which is currently called RingCT. It involves a range-proof, it involves commitments, there's different techniques used to show balance - we need ring signatures, for example. All of these things kind of interact and have different security properties.

And the idea is that a more complex cryptographic construction like RingCT or Seraphis or Spark, that's a transaction protocol for example, will use these kind of cryptographic building blocks like range proofs and linkable ring signatures, each of which have known and well understood security properties. And why this is nice is that it makes the analysis much more structured and straightforward. So to prove for example that RingCT is secure under a given security model or Seraphis or something like that, in order to show that those are secure, we can look at the security properties of the building blocks, like the ring signatures and the range proofs. And what's cool is that it makes it modular. So for example, we said that Monero has had three different range proof designs. Because Bulletproofs and Bulletproofs+, for example, have the same security properties - complete, sound, zero knowledge, it means if you're very careful about how you do it, you can pull one of them out and put a new construction in with the same security properties, which is what happens. When the Monero protocol moved from Bulletproofs to Bulletproofs+, Bulletproofs came out, Bulletproofs+ came in. We know that the security properties are the same between them, so the security of the RingCT overall giant transaction protocol is maintained. That's pretty cool.

And it turns out that when we talked about Schnorr signatures earlier and talked about proving security via unforgeability, it turns out you can actually use this similar stacking technique in order to prove even things like signatures, Schnorr signatures secure. So there's a whole lot of interesting stuff that you can do.

But wait, there's more. So they sound infomercial pitches or something. I did say that this is just one very small slice of how certain types of cryptographic systems are proven secure. And there is truly a whole universe of different techniques and approaches that you can use to prove security. This is just one very specific slice of one very particular methodology of proving systems secure. So this is by no means comprehensive. But there are some good takeaways even from this kind of small slice.

The first one is that a security proof is only as good as the security model. And remember the security model basically says: how would an adversary be allowed to interact with this system and try to break into certain defined properties. If your properties are defined poorly, or if your security games are defined poorly, the security proof doesn't really say anything about how your system will hold up in the real world. So when someone says: I've proven this secure, the first thing you should think of in your head is: under what security model? They cannot tell you. Probably a bad sign.

And what's also interesting is that even a valid security proof on a bad security model is technically valid, it just doesn't tell you much about the real world. Like you can use that CLSAG example. When at first keys had to be generated honestly, well, we proved it secure under that security model, but that doesn't necessarily tell you much about the real world. So changing the security model to be more realistic is helpful.

The second one is that some common and general security properties are useful, as we said, because they help you take simpler systems, like a range-proving system, for example, or a linkable ring signature, and stack them up into more complex systems, like a transaction protocol, which is much broader.

And the third, and I think final and biggest takeaway, is that formal security is very, very tricky. Formalizing these things, building games, putting security proofs together is really, really subtle. And it's very easy to get wrong in very subtle ways. But it's very essential for building robust systems. So those I think are kind of the big takeaways about what it is to be a secure system and formulate a good security model.

We have one minute left, so thank you. These slides will be up on GitHub after the conference if you'd like to review them or shake your fist at them or whatever. And definitely email me or come find me afterwards if you have questions, so thanks.