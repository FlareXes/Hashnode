---
title: "Why WhatsApp's End-to-End Encryption Is a Joke (and Telegram Might Be a Better Option)"
datePublished: 2026-11-08T12:37:17.490Z
cuid: cmw7thl5s000007l5fm9seygt
slug: why-whatsapp-s-end-to-end-encryption-is-a-joke-and-telegram-might-be-a-better-option
cover: https://cdn.hashnode.com/uploads/covers/620721c5a2be760e35394d88/08bee6b5-fba5-4ebc-b695-806a9d5e9c64.png
tags: security, privacy, whatsapp, telegram, end-to-end-encryption

---

Many privacy and security experts will tell you the same thing: if it comes down to Telegram versus WhatsApp, pick WhatsApp. I'm not going to talk about Signal or Session here. They're better options and we all know it. This post is about the two giants that most normal humans actually use.

And yes, it sounds counterintuitive. How can Telegram be *more* secure when WhatsApp has end-to-end encryption (E2EE)?

I'm not going to play the "WhatsApp is closed source, so who knows what's going on in there" card. That's a known quirk of closed versus open source, and I won't double down on it. The real problem is bigger, and it's wonderfully boring: **backups**.

## How the two actually work

**Telegram** uses cloud-based encryption by default for most chats. Your chats live on Telegram's servers, encrypted with AES-256. That's why you can open the app on your phone, your laptop and your toaster, and everything is already there. Your data is basically always backed up, so Telegram doesn't even have a backup option. It doesn't need one.

**WhatsApp** is end-to-end encrypted, which means your data stays on your device and never goes to the cloud. The catch is that WhatsApp can't sync your messages across devices the way Telegram does, because the data is local unless you enable backups. And WhatsApp *really* wants you to enable backups. It uploads your chats to Google Drive or iCloud, and that's where things get ugly.

## The problem: your "secure" message takes a little trip

So picture the journey of your "secure" message. WhatsApp takes your end-to-end encrypted data, rips off the armor, puts it in a cardboard box and ships it to Google Drive. It's a disaster.

"But I never enable backups!"

You may be sitting at home right now, yelling at the screen: *I am a privacy god! I simply do not turn on backups! I have outsmarted the system!* Congratulations, genius. Now, what about the person you're talking to?

End-to-end encryption requires **two ends**. You might have your device locked down like Fort Knox, but your friend, your mom or your co-worker may have backups on. You never gave permission for your side of the conversation to be uploaded, but because they did, your privacy is vaporized. You are only as secure as the dumbest person in your group chat, and we all have that one friend who clicks yes on every pop-up. They are probably backing up their WhatsApp to a public Dropbox folder right now.

Telegram doesn't have this problem. Your data goes to Telegram's servers encrypted, by default, and nobody's cousin gets a vote. So the question becomes:

> Do you trust your friends and family with your data, or the company that built the product?

I'd trust the company more. At least they'll do *some* sort of encryption and follow industry-standard practices. To be fair, Telegram isn't following the industry standard either, because E2EE should be the default in 2026. But when it comes to storing my data, they're doing the better job.

## "But WhatsApp has encrypted backups now!"

True, and credit where it's due. WhatsApp introduced end-to-end encrypted backups back in late 2021, protected by a password or a 64-digit key that only you know. It's a good feature. But it's an optional extra layer and it is not enabled by default. WhatsApp pushes you to back up your chats, but it doesn't push you to *encrypt* them, so you're handing your friends the liberty to choose, and most of them will choose "whatever the app did by default."

I checked with 5 people around me who use WhatsApp. Three of them are into tech: AI, security, web development. They know how things work. They checked, and **all three** had backups running to their drives, unencrypted. If the tech people aren't enabling it, what chance does your aunt have?

### How big is this, really?

Honestly, it's blurry. Nobody has surveyed this properly. Estimates of how many people use WhatsApp backups swing wildly depending on the country, from as high as 70% to as low as 10%. One Forbes article quotes WhatsApp itself saying they have **billions of active users** (around 3 billion) and that most of them don't use chat backups. But "most" is not a number.

So let's be generous and say only 10% do. That's **300 million people**, which is bigger than most companies and most countries.

There's also an irony here. In a way, WhatsApp is telling billions of users, "please don't lose your phone, you'll lose your data," and pushes backups hard. But it doesn't push the security option that makes those backups safe. Backup should be a thing, sure, but encryption is a security option, and when you build a product around E2EE, you have to think around it.

My view: WhatsApp can fix this by **enabling encrypted backups by default**. I'll go further and say that if you back up your data, encryption should be a requirement, not an option. Otherwise you're exposing not just your own data but also the person you're talking to, without their consent.

## Other cracks in the wall

There are more problems with WhatsApp's E2EE, but I don't want to go into details. A few points are worth knowing, though.

First, E2EE only protects data in transit. The app that decrypts it can still see everything. Telegram's app can too, in theory, but Telegram publishes its client code and supports reproducible builds on Android and iOS, so you can build the app yourself and match the result against what's in the store. You can't say that for WhatsApp.

Second, there's the matter of keys. When your contact reinstalls the app or switches phones, WhatsApp generates a new encryption key for them, and your app just starts using it. That's normal. But researcher Tobias Boelter showed back in 2017 that, by default, nobody tells you about it. In theory, a malicious (or compelled) server could swap in a key it controls, and you'd never notice. You only get a warning if you manually turn on "Show security notifications" in the settings, and even then you're told after the message has already been sent. WhatsApp's answer was that this is by design and not a backdoor, and I'm not claiming there's a smoking gun here. But it's exactly the kind of thing I can't verify, because I can't read the code. Telegram's cloud chats don't have this issue, but only because Telegram holds the keys anyway, which is its own trade-off.

Third, there's the report button. When you report someone on WhatsApp, WhatsApp receives up to the last five messages you got from them. I understand how this works. It doesn't break the encryption; the reporter's device just forwards the decrypted messages, and WhatsApp itself says this isn't at odds with E2EE. I don't mind it that much, because it exists to stop criminal behavior. But when you market yourself as end-to-end encrypted, the fine print starts to matter. (That's also why Signal has no proper report option.)

Fourth, Telegram is worse here, though. It can read your cloud chats anytime it wants, because they aren't end-to-end encrypted at all. They've also said they'll act on legitimate government requests involving things like terrorism, such as reporting chats or blocking channels. That's reasonable on its own, but it's a reminder of who holds the keys.

If you check [securemessagingapps.com](https://securemessagingapps.com), you'll see WhatsApp collects more red flags than Telegram, even though Telegram can read your messages.

## "But the experts recommend WhatsApp!"

A lot of well-known privacy voices recommend WhatsApp over Telegram. The usual reason is simple: WhatsApp is end-to-end encrypted and Telegram isn't. That's a fair point on paper, and I'm not here to argue that Telegram is secretly the more private app. What bothers me is that the recommendation usually comes without the trade-offs. Just because WhatsApp is end-to-end encrypted doesn't mean it's safe to use.

It's better than SMS, definitely. But is it better than Telegram's approach? That's a question of trust and your threat model. Where I start to get cautious is when I see a feature like cloud backups combined with a closed-source app. I'm not being paranoid. They're both fantastic apps at this scale, and I'm just laying out what can go wrong.

## What the Telegram CEO has to say

Pavel Durov, Telegram's founder, has been asked many times why Telegram isn't end-to-end encrypted by default. His argument, roughly, is that Telegram is a feature-rich platform (huge groups, channels, multi-device sync, cloud storage) and that making everything E2EE by default would cripple those features. People who want maximum security at the cost of convenience, he says, can use Secret Chats. The other half of his argument is about scale: apps with default E2EE, he says, can't really scale, which is why Signal and Session stay niche instead of going mainstream. Add Telegram's huge feature set on top of that, and in his view default E2EE gets even harder. You can read his posts on this, for example *"*[*Why isn't Telegram end-to-end encrypted by default?*](https://telegra.ph/Why-Isnt-Telegram-End-to-End-Encrypted-by-Default-08-14)*"*

Durov has also gone after WhatsApp directly, calling its "E2EE by default" claim a "giant consumer fraud" because most private messages end up in plain-text cloud backups. "Fraud" is a stretch, since WhatsApp's messages really are E2EE in transit, but the backup problem is real, and it's the whole point of this post. It's just a little convenient coming from the CEO of an app that doesn't offer E2EE by default either.

I agree that scaling E2EE is harder. I don't agree that it can't be done, because WhatsApp did it, and Telegram, with its existing user base, could too. Signal isn't niche because of E2EE. It's niche because WhatsApp had the first-mover advantage and Telegram caught on through features, so people just don't know Signal. An E2EE app *can* become big. It's difficult to build and maintain, but it's not impossible.

And to give WhatsApp its credit: implementing E2EE at that scale is impressive. But it's messy. There's a lot of speculation because we can't see the source code, and it has features that shouldn't exist in an E2EE model, like unencrypted backups. Fix those and it's pretty much there.

## Why I choose Telegram

If Telegram had default E2EE, I believe it would be the best messaging app out there for the general public. Even without it, Telegram still wins for me, and it starts with the boring, important things. The official apps are open source, Android and iOS support reproducible builds, and when I delete a message it actually gets deleted instead of living forever in somebody's cloud backup.

Its privacy practices are also better than WhatsApp's for everyday use. Telegram doesn't sell my data, doesn't collect heavy amounts of it, and doesn't share it with third parties unnecessarily, while WhatsApp collects plenty and shares it within the Meta family. On top of that, it has more features, a better UI and far less clutter, and it isn't obsessed with the commercial side of things. It also had usernames that hide your phone number way before WhatsApp or Signal got around to them, which still matters a lot to me.

Then there's the practical stuff. Telegram has official apps for everything: Linux, Windows, macOS, Android, iOS and the web. It also lets me delete my messages for everyone with no time limit, unlike WhatsApp, so whenever I'm done with a conversation I just delete it. I also keep auto-delete on, set to 1 day.

And a small experiment for the nerds: two days ago I whitelisted just two IP addresses on my phone and blocked every other Telegram connection. It kept working. Messaging and the UI loaded just fine (I didn't test file uploads). I'm pretty sure you can't say that about WhatsApp.

So where does that leave me? On **Telegram's trust boundary**. I'm choosing to trust Telegram to keep my data encrypted and not share it with anybody. WhatsApp's closed source means everything they say is speculation, they share data with third parties, and the only extra thing they give me is E2EE. Don't get me wrong, I prefer E2EE, and I don't back up my chats, so my side of the conversation stays safe. But that one feature isn't enough to make up for everything else.

I'd love Telegram to add E2EE by default, but I don't see it happening in the next five years, because they believe it isn't really important. Which is absurd.

## "What about secret chats?"

Glad you asked, because I know you would.

If WhatsApp's E2EE is a joke, Telegram's secret chats are a bigger one. If something isn't on by default, people won't use it. Secret chats also don't work in groups, and they're only available on the mobile apps and the native macOS app, **not** on Telegram Desktop for Windows or Linux and not on the web. It's a bit like a panic room that only exists in half your house.

## Final thoughts

If you truly care about privacy and security, **I don't recommend either of these.** They're built for the general public, not for activists or anyone with a higher threat model. Go with something much more secure, and check [securemessagingapps.com](https://securemessagingapps.com) to pick an app that fits your needs.

So, a quick recap. We started with WhatsApp's unencrypted Google Drive backups being a disaster that ruins E2EE. We talked about WhatsApp's report button that quietly hands over your last five messages, and Telegram's open-source client versus WhatsApp's closed-source black box. We roasted Pavel Durov's excuse for not doing E2EE, and finally concluded that Telegram's Secret Chats are a useless novelty. Did we miss anything?

Trusting any of these apps is basically choosing your favorite flavor of poison. But hey, at least Telegram lets you delete the poison without leaving a tombstone.

Now go check your WhatsApp backup settings. I'll wait.