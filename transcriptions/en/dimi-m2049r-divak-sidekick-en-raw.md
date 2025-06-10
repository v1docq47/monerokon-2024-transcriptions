# Dimi 'm2049r' Divak

_**SideKick!**_

_Following last year's PocketChange, there's a new Monerujo feature for the hardcore monerians to battle test, live!_

_SideKick allows users to initiate transactions on their regular phone and then sign them on another spare phone that's kept offline. It works as a hardware wallet, minus the closed source and the data leaks._

_This talk delves into the technical aspects of SideKick, explaining how it works and the reasoning behind some of the team's design decisions and challenges._

[https://youtu.be/l7SuVqfbKR4](https://youtu.be/l7SuVqfbKR4)

---

_**Dimi:**_ Today we'll be talking about SideKick. After two years of development, two and a half, no, three years, three years ago we had the idea to make a SideKick. And we'll be talking about what that is, why that is, and how we've decided to implement it.

So SideKick is a hardware wallet for the masses, just like Monorujo is a Monero wallet for the masses. So with that idea moving on, we said: "Okay, we want to provide an alternative to standard hardware wallets for normal people to use". It was very important to me to make it in such a way that it's not only bound to Monorujo but so that anyone can use it. So any client can integrate to it. And that's why it's implemented in the Moneo core code. So one of the follow-up things that we'll be doing is making a pull request for that. It needs to be cleaned, of course, reviewed and all that kind of stuff. We finished it yesterday at two, and at three we bought a cap with SideKick so it works.

Yeah, so this is just a screenshot of the Wallet2 API where we've added the device SideKick number three next to Ledger and Trezor which you all know.

But why did we do it? As I said, we wanted for the masses. Why don't people go out and buy Ledgers, for example? You have much better answers than me for this question. Our answer is: it's super difficult for a lot of people to actually get hardware, especially over the Internet with customs which they know that you have such a device: "Why do you have such a device? It's forbidden", probably, or maybe in your jurisdiction, the companies know that you have this, you've got a target on your back. We've had leaks of people's home addresses six years ago where they had a reason for buying a hardware wallet. How much is that worth today? The target on your back is getting bigger and bigger every day, thankfully. Yeah, so privacy issues. And we're Android people. And we said that actually Android is a good choice for making this hardware. Again, not a dedicated hardware but an off-the-shelf Android device which you have lying around, maybe in your box of cables like in the very first slide. I have at least three lying around. Now I have a purpose for them for testing SideKick.

So it's based on the Ledger workflow. We integrated Ledger a long time ago into Monerujo, and the Ledger workflow is very well documented. The Monero specific code is on GitHub, is also fairly well documented. The code is nice and legible so it's super simple to understand how it works, especially in combination with how the Monero code works, which is also pretty straightforward.

What we use is, and with Bluetooth communication between this SideKick device and your normal client, so Monorujo and SideKick talk over Bluetooth. Bluetooth itself is encrypted and even if it wasn't, the communication itself can be in plain text basically or sent to the clearnet without any kind of encryption, because no secrets pass between the two devices. So all the secret information, all the secret keys, all the secret calculations - all the calculations would have secrets as outputs - stay on the SideKick device and are referenced by some kind of ID.

And the whole thing is implemented as a subclass of hardware device which basically ensures that it has the same API as any other hardware wallet or even the software. Wallet is implemented in such a way so when you start Monero client on your PC, it uses the same API to talk to its software counterparts. So this gives us the opportunity to say: you can use this, anyone can use this, you can implement it in the Monero GUI client and any other, Cake Wallet for example, any other wallet. And all they need to do is ensure that they can speak this Bluetooth protocol, because that's very system specific, so, yeah.

So as you see here, this is just a couple of functions so that one can get an idea what goes over the line. So basically, things to verify keys and multiply scalars, and generate derivations and that kind of stuff.

On the next slide, we see on the left the SideKick opened wallet. So basically what we do is we generate an absolutely normal Monero wallet, like you use someone's new wallet, you get your keys file, and that's the end of the story on that side. But you can make a backup of that wallet, and you can import it into your Monorujo or your desktop client, and it's basically a normal Monero wallet.

So you open this thing on the SideKick and you connect your, in this case, Monorujo, over Bluetooth with it. The two devices need to be paired before because that's how Bluetooth works. And then you restore, basically on the right phone, you restore the wallet, which is the SideKick wallet, just like you would with a Ledger. So if you imagine the left side being a Ledger or a Trezor, It's exactly the same process. So what you have basically on the right side are all the transactions, the connection to the Internet, the cache file basically of your transactions, while the keys remain solely on the left side. For now we are always transferring the view key. They will add an option later not to transfer the view key, but personally I've never heard anyone not doing that because which just takes forever. And maybe that's not the right use case. So yeah, I think that's that.

A lot of talk has been about threat models, and how can this be attacked and it was in the beginning and probably still there's a lot of backlash about Bluetooth, EVO and that kind of stuff. Bluetooth is super simple to use. Maybe it's insecure. The way I understand it is that the Bluetooth attack vector is targeting a faulty Bluetooth stack on the phone so that an attacker can get in become root and do evil things. If that's your threat model, then don't use this, use a paper wallet.

The way that SideKick is built, it uses the same encryption of the keys as Monorujo with this crazy pass, which is its own topic, but basically what it uses is signing by the secure element on the Android device where the key is not accessible at all, it's inside the device. So what we create is a hash use actually a signature of your password that you use for the wallet. We create a signature, we hash that signature with the CryptoNight slow hash function so that it creates then in the end a 256-bit key and that is used as the password for your keys file.

So if you don't have a screen grabber to actually see what you're typing and a root on that device so you can actually access the secure element for that particular app. Then you would need to use that device and brute force something which is just impossible to brute force. If you can do that, I don't need to do any of this, I can just guess your wallet basically, right?

Yes. I think that's it in a nutshell. We have the two APKs, if you want to try it out, which a couple of people did yesterday. On the left side you have the Monorujo QR code. Maybe we should have written down where these points, they point to monorujo.app slash monoruyo.apk, and the last one is to sidekick.apk. Yeah, give it a try.

Thank you.
