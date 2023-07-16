---
title: "Cryptography for Developers and Best Practices"
seoTitle: "Cryptography For Developers & Best Practices"
seoDescription: "Sometimes it is misunderstood that developers should know the maths behind the cryptographic algorithm. Which is such a myth. They don't need to worry about"
datePublished: Sat Jul 01 2023 23:30:39 GMT+0000 (Coordinated Universal Time)
cuid: cljkmzfzj07xhzznv29s44sdb
slug: cryptography-for-developers-and-best-practices
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1688258379447/a3923b1f-b9be-430f-883f-2b205bd8a6ae.png
tags: python, security, developer, best-practices, cryptography

---

# Why Cryptography Only For Developers?

Sometimes it is misunderstood that developers should know the maths behind the cryptographic algorithm. Which is such a **myth**. They don't need to worry about any maths to use any cryptographic algorithm. In reality, developers should just use them like other algorithms by importing them from libraries. Many developers also want to stay away from Cryptography.

After all, they think it's complex and doesn't need to worry about it because they have good programming skills. But if I ask you, are you sure that you will never implement any authentication system in your life, you will never manage customers' sensitive data, or do you think somebody will hire you for writing insecure code? I think your answer would be **NO**. Any good organization will not just ask you about your programming skills. A good developer or programmer also knows how to write well-optimized, well-designed, and well-secured code. And it's not hard at all. If you think so, then just read or listen to this blog post for better exposure to Cryptography for developers.

# Basics of Cryptography

Before we get started with the developer side of cryptography, we need to understand a few basic things that are common across all the applications of Cryptography. In the next few sections, we will see why Cryptography is even needed. Misconceptions that we usually have, and what are the majority of things we use as developers?

Before I go any further, I want you to know that this is only the tip of the iceberg in cryptography, but it's almost (60% to 80%, this is absurd) a complete iceberg for developers. Whatever I'll explain here is my experience, and I'm not an expert in Cryptography. So, feel free to correct me in the comments section.

## What is the Cryptography?

If you're completely new to this. Then there is a definition from Wikipedia: *Cryptography is about constructing and analyzing protocols that prevent third parties or adversaries from reading your private messages. Modern cryptography exists at the intersection of mathematics, computer science, information security, electrical engineering, digital signal processing, physics, and others.* Easy enough, ya! I know that.

So, Just getting good at maths isn't gonna make you good at cryptography. You have to think about other things too. But, as developers, we only need two things from this definition. First, **prevent third parties from reading private info** and second, **computer science**. This part we already know.

### How does cryptography secure data?

Not mathematically but what does cryptography actually do to achieve data security? So, The answer to this question is that the core concept behind cryptography is to provide **Data Confidentiality**, **Data Integrity**, **Authentication**, and **Non-repudiation**. Some books also include **Authorization**. So, let's look at them one by one.

#### **Confidentiality**

We consider a piece of information confidential when it is only accessible to the intended individual. This is usually achieved by encrypting the plain text into ciphertext (encrypted text is called ciphertext). For example, A Doctor is the only person who should look at a patient's health report nobody else, to maintain Confidentiality. Or If `A` sends a message to `B` then `C` can't access it, No matter what. In real life, **End-To-End** encryption is the best example in messaging apps.

#### **Integrity**

To avoid any alteration in resources or information, we implement an **Integrity** mechanism. For example, `A` sends a message to `B`, and `C` can't read it but `C` can intercept it, change the message into something else and then send it back to `B`. Data Integrity is also important to make sure that information wasn't lost or corrupted in-transmit. So, to make sure data wasn't corrupted, manipulated, or lost in-transmit we can implement an **Integrity** mechanism that could be [**Message Authentication Codes (MAC)**](https://en.wikipedia.org/wiki/Message_authentication_code) or [**Checksum**](https://en.wikipedia.org/wiki/Checksum), depending on the use case.

#### **Authentication**

We can say authentication means proving *Who Are You?* We have lots of examples of Authentication in the real world like Login Page, Forget Password, 2FA, etc. where you have to provide relevant information to prove *Who Are You?* Authentication can be achieved in different ways, but the majority of authentication methods include things like -

* **Something You Know**, Like Password
    
* **Something You Have**, Like Credit Card
    
* **Something You Are**, Like Biometric
    

#### **Non-repudiation**

Imagine your friend sends a message to you and after some time he denies that he didn't send anything to you. So, to avoid these situations where an individual can't deny the validity of certain actions, we implement a Non-repudiation mechanism. This can be done using [**Digital Signature**](https://cloud.google.com/kms/docs/digital-signatures).

#### **Authorization**

Authorization is an implementation to check whether a user is allowed to access a resource or not. Personally, I don't think this should be part of Cryptography. I just included this because few books refer to this in cryptography (may they try to relate cryptography with security) but, it's just a security mechanism e.g. **Access Control List (ACL)**. And, don't get confused between **Authentication** and **Authorization**. For example, You work in a Data Center, so you can enter the building this is Authentication. But, you work at Help Desk thus you are not allowed to enter in server rooms and that is Authorization. That's it, we won't be discussing it anymore.

## Type of Cryptography

### Private Key Cryptography

Also, known as **Secret Key Cryptography** or **Symmetric Key Cryptography**. Where a single key is used to encrypt and decrypt the data. For Symmetric cryptography, **AES-256** (Advanced Encryption Standard) is the standard for encrypting data. Know more about AES working here: [The Applications Of Matrices In Cryptography](https://flarexes.com/the-applications-of-matrices-in-cryptography).

```java
To Encrypt: 
            Secret-Information  x  Key  =  Ciphertext

To Decrypt: 
            Ciphertext  x  Key  =  Secret-Information

-----------------------------------------------------

Same key is used for both encryption and decryption.
```

### Public Key Cryptography

Also known as **Asymmetric Key Cryptography** is the exact opposite of **Symmetric Key Cryptography** here a key pair is generated. A Key-pair is the combination of a **Public Key** and a **Private Key**. In most cases, Public Key is used to encrypt data, and Private Key is used to decrypt data. For Asymmetric cryptography, **RSA** (Rivest-Shamir-Adleman) is standard for encrypting data. Due to this nature, we can achieve things like End-To-End encryption.

```java
To Encrypt: 
            Secret-Information  x  Public Key  =  Ciphertext

To Decrypt: 
            Ciphertext  x  Private Key  =  Secret-Information

-------------------------------------------------------------

Different keys are used for both encryption and decryption. 
Public  Key for Encryption.
Private Key for Decryption.
```

### Which one is better Symmetric or Asymmetric Cryptography?

Asymmetric Cryptography, Yep straight-up Asymmetric Cryptography provides better security than Symmetric Cryptography. But still, this is not usually the best to encrypt data. Let's understand this.

* Symmetric Cryptography is more resistant to quantum computing.
    
* Symmetric Cryptography is suitable for encrypting/decrypting large data.
    
* Symmetric Cryptography algorithms are easy to implement in the codebase.
    
* Symmetric Cryptography is certainly, faster than Asymmetric Cryptography.
    
* Symmetric Cryptography is also used for securing military-grade protection.
    

Even **AES** is also known as military-grade encryption that hasn't been broken till now (if implemented correctly). So, Symmetric Cryptography is so great then, why did you say "Asymmetric Cryptography provides better security than Symmetric Cryptography"? Well, that's true though, Asymmetric Cryptography is one of those who say less and do more.

Asymmetric Cryptography is relatively slower than Symmetric Cryptography. But It is used to transfer your keys for Symmetric Cryptography. The best example from Wikipedia: RSA is used to transmit shared keys for symmetric-key cryptography, which are then used for bulk encryption–decryption. In a security context, Asymmetric Cryptography has more flexibility and brought spectrum than Symmetric Cryptography. Let's look at a few examples.

Without Asymmetric Cryptography

* You can't use messaging apps like Signal and WhatsApp securely, [End-To-End Encryption](https://en.wikipedia.org/wiki/End-to-end_encryption).
    
* You can't browse internet securely, [Certificate](https://en.wikipedia.org/wiki/Public_key_certificate).
    
* You can't solve key [distribution](https://en.wikipedia.org/wiki/Key_distribution) problems in both types of cryptography, [Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange).
    
* You can't achieve Non-repudiation, [Digital Signature](https://en.wikipedia.org/wiki/Digital_signature).
    

and the list goes on...

> **Note:** Integrity, Authentication, and Non-repudiation mechanism can only be possible via Asymmetric cryptography.

## Encryption vs Hashing vs Encoding

Sometimes people get confused between these different terms. Or they use them interchangeably, even technical individuals. So let's first clear this confusion with these few lines I always say when explaining this to anyone.

1. Encrypted Data Can Be Decrypted.
    
2. Hashed Data Can't Be De-hashed.
    
3. Encoded Data Can Be Decoded.
    

### Encryption

Encryption is used to secure data, communication with a key or password. Once data is turned into ciphertext with a key, we will need the key again in the future to get the data back. That means **Encrypted Data Can Be Decrypted**. We already discussed encryption above, its working, and its types. So, we'll move on to hashing.

### Hashing

Hashing is the process of transforming any given data into a fixed-sized string. It is a non-reversible or one-way process. That means, **Hashed Data Can't Be De-hashed** (Theoretically). In hashing, the same input always outputs the same hash or hash digest. And any minor change in inputs will drastically change the hash value. Different inputs (data) never give the same hash digest. If two different inputs end up generating the same hash, then it will be considered a [Hash Collision](https://en.wikipedia.org/wiki/Hash_collision). That's why it's recommended to always use the current Standard Hashing Algorithm (SHA-256, for now). We will talk more about it later. There are numerous use cases of hashing, like - Checksum, Message Authentication, Storing Passwords, etc.

As you can see below same input gives the same hash value.

```bash
  ~ printf "this is me" | sha256sum
dd81cb79f11bedd77be0000f28e264e2c4b42376c76b891c7845b172c071d631  -

  ~ printf "this is me" | sha256sum
dd81cb79f11bedd77be0000f28e264e2c4b42376c76b891c7845b172c071d631  -
```

But, If I make a small change (capitalizing the first character) it will drastically change the hash value.

```bash
  ~ printf "This is me" | sha256sum
89907528d197ac9b349a5798f802e9b571cda02062cd288a4f1641ecfb83925f  -
```

You can try this yourself on an online tool, [CyberChef](https://gchq.github.io/CyberChef/).

### Encoding

Encoding is just a different representation of the same data. For instance, 10, 1010, and 0xA represent the same value in different number systems: decimal, binary, and hexadecimal, respectively. The purpose of encoding is to convert data in a way that is compatible with other applications. For instance, relational databases can't store images, but you can convert that image into text format using some encoding algorithm, and then can be stored in a database. Like hashes, encoding also doesn't require a key to encode or decode.

Let's look at how a file can be converted to base64 encoding.

```bash
  ~ cat scroll_by_me.js | base64
bGV0IHNwZWVkID0gMTsKbGV0IGNsaWNrID0gZmFsc2U7CmxldCB0ZW1wOwoKZG9jdW1lbnQub25j
bGljayA9ICgpID0+IHsKCWNsaWNrID0gIWNsaWNrOwoKICBpZiAoY2xpY2spCgkgIHRlbXAgPSBz
ZXRJbnRlcnZhbChmdW5jdGlvbigpeyB3aW5kb3cuc2Nyb2xsQnkoMCwgc3BlZWQpOyB9LCAyMCk7
CiAgZWxzZQoJICB3aW5kb3cuY2xlYXJJbnRlcnZhbCh0ZW1wKTsKfTsK
```

Now, you can go to [CyberChef](https://gchq.github.io/CyberChef/) and try to decode this.

## Key Derivation Function

KDF or Key Derivation Function is a cryptographic algorithm that is used to derive one or more keys from a primary secret (a master key or a passphrase), [Key Stretching](https://en.wikipedia.org/wiki/Key_stretching) to make weak keys more secure or to increase computation cost. This provides resistance against brute-force attacks or pre-computed rainbow table attacks. Like Hashing, KDF algorithms also generate deterministic output or one-way output. We can't reverse it.

### When to use KDF over Hash?

Let's understand a few similarities between Hashing and Key Derivation Function. They both look and work pretty much the same, though they serve different purposes, which is crucial to look into.

Hash functions are pretty fast at calculating the hash digest (hash value) of large amounts of data. Which is best suited for ensuring integrity and message authentication. But sometimes we want things to be slow in the cryptography world. Let's see why.

Imagine, you have a SHA-256 hash digest `31c27648b8f72727ef96806d541957746c0c005268609822a7d8fa1e3ac805f8`. you want to calculate its original value. But you may be thinking, "How is it even possible? Hashing is a one-way algorithm?". Well, the answer is simple: we'll brute-force. For instance, I'll use a dictionary to convert each word into SHA-256 and then compare it with the original hash value. If the values match, then I found the original text of the given hash value. Second, I can use online tools like [CrackStation](https://crackstation.net/) which already holds precomputed hash tables. Use [CrackStation](https://crackstation.net/) and comment the original text of the above hash.

Now imagine again if that hash was a password. And that is where the problem begins. Hashing algorithms aren't computationally heavy. They are designed to be fast, but in the case of a higher-security model, we prefer KDF. Cracking these kinds of hashes in the world of cloud computing is often very easy. Hackers use tools like CrackStation, John The Ripper, Hashcat, etc. That can even utilize the power of GPUs and parallel processing.

In those scenarios, KDF really stands out. KDF algorithms are essentially designed to be slow and computationally expensive. They drastically reduce the success rate of attacks like brute-force or precomputed hash tables. You can also tweak KDF algorithms to increase or decrease computation power. **PBKDF2** (Password-Based Key Derivation Function 2) is a widely used Key Derivation Function, but there are also better options available, like **Scrypt** which also provides GPU and parallel processing resistance.

So, the Moral of the story is - Sometimes things being slow are okay.

> **Note :** In Many places, you'll find KDF categorized as Hash.

## Random Number Generator

Random Number Generator in short RNG plays a significant role in cryptography. You will encounter RNG in many situations in cryptography. Like - To encrypt data you need RNG, to use KDF you need RNG or to generate keys, again you need RNG. But, Not just any RNG. A wrong choice can lead to potential loopholes in implementation.

There are two types of Random Number Generators. First, [**Pseudo-Random Number Generator** (PRNG)](https://en.wikipedia.org/wiki/Pseudorandom_number_generator) and [**Cryptographic Pseudo-Random Number Generator** (CPRNG)](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator).

### Pseudo-Random Number Generator

[PRNGs](https://en.wikipedia.org/wiki/Pseudorandom_number_generator) are those algorithms that generate a random number or sequence that only looks random. But in reality, it's not random at all. What if I say, the libraries you use in your codebase to generate random numbers, choose a random element, shuffle a list, etc, are not random or only look random. And, When we think logically *How is it even possible in the world to generate a random number from a deterministic machine?* Computers only understand deterministic values or simply maths. So, the answer is they don't, they just use some **Initial Value** that only looks random. For instance, the number of processes running on the machine or time. Many PRNG implementations use time as an initial value. Let's understand this by an example

```plaintext
time = 1686919931823062166 in nanoseconds
random_number = time * 34 / (439 ** 2) % 10

random_number will be 9.5625
```

Now, this will generate a new random number every time you run it, but is it really a true random number? No, it's not. It just looks random in nature. So, it's not a good practice to use PRNGs for security purposes. PRNGs are best for games or simple tasks, but not for security stuff; for that we have CPRNG.

Python's `random` module documentation shows a big red warning box stating - `Warning: The pseudo-random generators of this module should not be used for security purposes. For security or cryptographic uses, see the secrets module`. This is a good example of always refer to the documentation. More on that in the [Never Assume, Refer To Documentation](https://flarexes.com/cryptography-for-developers#heading-never-assume-refer-to-documentation) section.

> **Random Fact**: Python uses *Mersenne Twister* as PRNG in random module.

### Cryptographic Pseudo-Random Number Generator

[CPRNGs](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator) are algorithms that are suitable for security purposes. CPRNGs are seeded with good-quality randomness to avoid future predictions, like keyboard typing speed, mouse position, moving speed, radio decay, IOs (input/output), etc. These events are hard to predict. VeraCrypt (an encryption utility) uses mouse movements, to generate a good-quality random seed. Every programming language or operating system has different implementations of generating CPRNGs; therefore, it is not possible to mention all of them here. That's why it is recommended to refer to the documentation of the source (Programming Language, OS, Hardware, etc) that you're using to generate CPRNG. But here are a few examples: on Linux, you can use `/dev/urandom` and in Python, you can use `secrets` module. At last, CPRNGs take more time to compute a good-quality seed than PRNGs.

# Best Practices To Follow In Cryptography

Up until now, we have already discussed basic terminologies and things that are commonly used in development. And now we will see important rules or bullet points that should always be considered while working with cryptography. These are some best practices to follow while selecting or implementing a cryptographic algorithm.

## Never Use Own Crypto Algorithms

Ya! This has been seen very often that newcomers usually start writing their own cryptographic algorithms, which is just BAD. Even the best security organizations don't write their own algorithms to secure users' data. Instead, they use existing once that are trusted and analyzed over the years or even decades.

Bitwarden password manager is a fantastic example; they don't write any cryptographic algorithms to protect users' data. They solely import popular, reputable crypto libraries that have been tested over time by cryptography experts, [Verify Here](https://bitwarden.com/help/what-encryption-is-used/#invoked-crypto-libraries). Bitwarden doesn't even modify the existing algorithms. They just invoke them. This has also been spotted lately: developers or those new to cryptography often tend to modify existing crypto algorithms to make them more secure, like encrypting data twice, trying to change the key size, or worse... manipulating mathematics because they think their calculations are much better. But end up making it vulnerable or redundant.

Bruce Schneier once said "Anyone, from the most clueless amateur to the best cryptographer, can create an algorithm that he himself can't break." also known as **Schneier's Law**.

I'm not saying you should never write a crypto algorithm otherwise who will give us more good crypto libraries and algorithms? I'm just saying, don't use them in production if it doesn't satisfy all [Rules of Thumb](https://flarexes.com/cryptography-for-developers#heading-rules-of-thumb).

You should definitely read this short article by Bruce Schneier If you want to be a good Cipher Designer in the future - [Memo to the Amateur Cipher Designer](https://www.schneier.com/crypto-gram/archives/1998/1015.html#cipherdesign).

## Never Assume, Refer To Documentation

I've mentioned *Documentation* a few times in this article because it's essential. Any cryptographic algorithm you import from libraries will have its own implementation rules. Different libraries will have different default values, different suggestions, and different implementation methods. For instance, let's look at a Python cryptographic module or library called [PyCryptodome](https://www.pycryptodome.org/src/introduction).

If you search on the internet about Python encryption, you'll probably see some examples with `pip install pycryptodome` and some with `pip install pycryptodomex`. Then, which one should you be using? The answer is simply to look at the documentation. You'll see `pycryptodome` is a drop-in replacement of the old PyCrypto library, and `pycryptodomex` is independent of the old PyCrypto library. So, if your project already depends upon `PyCrypto` then you would prefer `pycryptodome` instead of `pycryptodomex`.

One more example could be that I want to use `Scrypt` as my [Key Derivation Function](#key-derivation-function). But this KDF requires a few arguments like - `N`, `r`, `p`. And you don't know what they mean or what the value of these arguments should be. So, you'll again refer to the [PyCryptodome Scrypt KDF](https://www.pycryptodome.org/src/protocol/kdf#scrypt) section from the documentation. Then, you'll see

* N means CPU/Memory cost
    
* r means Block size
    
* p means Parallel computation
    

and, there default recommended values should be ( 2¹⁴, 8, 1 ) for interactive logins (≤100ms) and ( 2²⁰, 8, 1 ) for file encryption (≤5s).

## Stick with Cryptography Standards

If you don't want to have headaches deciding which algorithm to pick, which algorithm is good enough, and which algorithm is secure and tested enough, then always! Always stick with cryptography standards. Because an algorithm is only stated as a standard when it has been tested thousands of times by experts and has survived heavy, unexpected, and high tides of **TIME**. No matter how good an algorithm looks on paper, and also works in the real world. But if it hasn't surpassed the enough long time until it is no standard. Like [**Argon2**](https://en.wikipedia.org/wiki/Argon2), a hashing algorithm that even won the 2015 Password Hashing Competition, people still have sceptical views about it just because it is a relatively new algorithm.

Most importantly, if your boss says, "Why do you think it's a good algorithm for our product?" you can throw an answer like, "If the US government thinks that **AES-256** is good enough to secure their confidential data, then who the hack are you?"

Places where you can look for cryptography standards.

1. [Cryptographic Standards and Guidelines](https://csrc.nist.gov/projects/cryptographic-standards-and-guidelines) From NIST.
    
2. [Cryptography Standards](https://en.wikipedia.org/wiki/Cryptography_standards) From Wikipedia.
    
3. [Cryptographic Storage](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html) and [Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) From OWASP Cheat Sheet Series.
    

Fourth! My favorite is to look for well-known, trusted, and respected security products or organizations and see how they implemented the things I want to implement in my own codebase. For example, if I want to use a KDF function for key stretching in my own codebase, then which algorithm will I use and what parameters should I be using? Again, free promotion coming from Bitwarden because they did good job with the documentation. So, I'll look into Bitwarden's documentation to see what they used and why they used it. It's just one of the many examples.

Give it a shot and tell me which KDF function I should be using in the comment section: [Bitwarden KDF](https://bitwarden.com/help/kdf-algorithms/).

And of course, I'll verify the information from different sources. Like from NIST. And lastly, standards can change, so always keep an eye out.

## Stick with Trusted, Audited, and known Crypto Libraries

As we talked about cryptography standards, we also needed to discuss libraries. Because at the end you'll be using them. So, we must take special precautions while choosing crypto libraries. A developer should make sure that any library, not just a cryptographic library, is trusted and audited. You should give more priority to cryptographic libraries that come pre-packaged or are officially supported by the programming language you're using. Libraries that come pre-packaged with programming languages are often tested and audited by professionals serval times before shipping.

But, it's not common to find all cryptographic algorithms or functions in pre-packaged libraries. So, I'll give second priority to well-known, trusted, and multiple times audited libraries. In the end, you won't write any algorithms by yourself, as we discussed above. You have to put your trust in somebody, assuming that this somebody has done better work at security than you could have.

[OpenSSL](https://en.wikipedia.org/wiki/OpenSSL) and [Libsodium](https://doc.libsodium.org/) are two well-known, trusted, and multiple-time audited external libraries. Session, a secure messaging application, uses Libsodium as its encryption mechanism. Read more about [Session vs Signal](https://flarexes.com/session-vs-signal-something-better-then-whatsapp). You can look at the [comparison of cryptography libraries](https://en.wikipedia.org/wiki/Comparison_of_cryptography_libraries). Keep in mind, information on Wikipedia can be outdated or false, so cross-verify if needed.

### Buying Cryptography Libraries from 3'rd Party Vendors

Personally, I have very sceptical views about buying cryptography libraries from 3rd-party vendors or crypto library providers (IDK what to say them). I don't think they provide any value in terms of security, customer support, or money. On the other hand, I believe they reduce the security of applications. Why? Because the majority of these library providers are closed-source. So nobody can take a look at them.

Second, if I require vendor assistance for testing the implementation of the cryptographic library, I can simply hire a security engineer or cryptography specialist to conduct the necessary security checks on my behalf. And, in large organizations, you will always have one. So, I think it's just nonsense to make your code more vulnerable. Off the top of my head, I can't think of any software that does this.

By the way, these are just my opinions; I could be wrong. What are your thoughts on buying a cryptographic library? Comment 👇

## Stay away from new Crypto Libraries and Algorithms

I've emphasized **Time** a lot because it's important to understand that, in the end, **Time will show what's secure and what's not**. Cryptographic libraries are no exception, like cryptographic algorithms. They both have to prove themselves in an equal manner. All the rules we decided on above require time. A library and an algorithm can't achieve the status of **Trusted**, **Audited**, or **Standard** overnight, it takes time. So, they have to surpass the high tides, winds, floods, or any other disaster of **TIME**. **Scrypt**, **XChaCha20** or **Argon2** all sound better than current standard cryptographic algorithms. But still, the only thing that is stopping them from becoming the next standard is TIME.

Though the algorithms that I mentioned above can still be trusted because they're popular and even used by some organizations like - NordPass, another password manager that uses XChaCha20 for encryption, I just wanna make a point: stay away from new algorithms and libraries. Let them spend some time in the crypto industry.

## Stay with less restricted LICENSE

The simplest one, don't use closed-source libraries or algorithms. Stick with public crypto libraries and algorithms. Always check the LICENSE otherwise, it could lead to legal issues. Usually, a trusted library's and an algorithm's LICENSES are short and clear. Less restricted and open-source libraries and algorithms are often more battle-tested. As mentioned in [Memo to the Amateur Cipher Designer](https://www.schneier.com/crypto-gram/archives/1998/1015.html#cipherdesign) by Bruce Schneier, if an algorithm is patented, no one will analyze it for you (unless you pay them). Why should they work for you for free?

Simply try to select an algorithm or a library that is not patented, is not closed-source, and has minimum restrictions in terms of usage.

# Conclusion

That's it. You did it. See, it doesn't have to be a tough one, at least for developers. Whatever we decided above is also applicable to other parts of software development. Then why does cryptography have to be different? Well, my main goal wasn't just showcasing the code that you can find on the internet just by searching "cryptography for developers". My main goal is to make you understand why we use them and how we should use them. And what are the points to keep in mind while doing some crypto stuff?

And of course, I didn't cover lots of stuff like digital signatures, certificates, mac, etc. Because it was almost the iceberg for the developers, not the complete one. Remember when somebody says something like this in cryptography: "Hash can't be dehashed, Theoretically" or anything theoretically, That simply means the cost of doing something like this can be very high, Practically.

This took me the longest to write out of all the blogs I ever wrote. I hope you enjoyed it or found it helpful. Open to any discussion and suggestion in the comment section. Thanks for reading or listening!

See ya!