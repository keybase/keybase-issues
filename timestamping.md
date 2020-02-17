# Keybase Security Evaluation:

## Past Security Issues: 

KB001: Local Privilege Escalation in MacOS Clients Before v2.5.2 (2018.12.18)
KB002: Local Privilege Escalation in Linux Clients Between v2.3.0 and v2.8.0-20181017144748 (2018.12.18)
KB003: Buffer overflow in Windows filesystem driver before v2.12.3 (2018.12.25)
KB004: Local Privilege Escalation in MacOS Clients Before v2.12.6 (2019.01.30)

Mac and Linux, both being linux based at the command line, seem to be vulnerable to local privilege escalations.  Windows has had a buffer overflow security issue in the past.

[Keybase Server Security Overview](https://keybase.io/docs/server_security)

The keybase filesystem  (KBFS) Threat Model does assume the security of the Go Implementation of the NaCl cryptographic library.  Assumed security could be risky, I would want more information, but can't find any.

# Keybase "Risk of Data Loss"  as stated in the [KBFS Documentation](https://keybase.io/docs/kbfs)

At the time of this document, there are very few people using this system. We're just getting started testing. Note that we could, hypothetically, lose your data at any time. Or push a bug that makes you throw away your private keys. Ugh, burn.

So as one of our first testers: back up anything you put into Keybase's alpha, and remember: we can't recover lost encrypted data.

Also, if you throw away all your devices, you will lose your private data. Your encrypted data is ONLY encrypted for your device & paper keys, not any PGP keys you have.

# Security Model

At a high level, end-user devices are trusted and Keybase/KBFS/other servers are untrusted. On desktops, we run all KBFS processes as the current user and use OS-level secret stores, **but we don’t attempt to protect against other processes owned by the same user or root.**  <------------------------  That's risky and could open keybase up to potential tampering, perhaps if system time is adjusted and the computer is offline. 

The KBFS client doesn’t trust any data coming from Keybase or KBFS servers, and verifies any received data against the relevant users’ public keys. For the nitty-gritty details, see the KBFS crypto doc. In particular, the KBFS servers cannot see into the contents or structure of your (non-public) files.

That having been said, the KBFS servers know what users can access which data, and will only serve data to an authorized reader of a TLF with a valid session. Furthermore, they will only serve historical (archived) data to writers of a TLF, even public ones.

Each TLF is backed by an "implicit" Keybase team, and the private keys required to access the TLF are managed by this team; see the Keybase team docs for more details. When you provision a new device to your account, it automatically gains access to these keys, and thus the TLF data. When you revoke a device, a new key is created for the TLF and used to encrypt all future data written to the TLF, to ensure that the device cannot read the new data. Old data is not re-encrypted (though our servers won’t serve it to revoked devices).

# Another Security Disclaimer Pointing out "At your own Risk"

If you download something off of Keybase.pub, you're trusting (a) Keybase over https, and (b) that someone hasn't hacked into Keybase.pub. So it's a great site to explore, but if you're downloading something crucial such as a copy of the latest libssl or bitcoind or GPG Tools Suite.dmg from a friend, you should really just look in /keybase/public on your own computer. That will verify all the crypto and guarantee you're seeing the same signed bits they are. And it's easier.

# Performance

We've made no performance optimizations yet. There's a lot of low-hanging fruit.

## Running on a resource-constrained system

On desktops and laptops, KBFS makes liberal use of memory in order to cache recently-read data for performance. It also uses CPU and networking resources to pre-fetch data you are likely to access in the near future, such as recently-edited files or subsequent data blocks to data that was just requested. These optimization tradeoffs might not be desired on systems that don't have any extra RAM or CPU to spare. 

# 🚀 Keybase is now writing to the Stellar blockchain 🚀  <<---- it's blockchain based anyhow! ......

Every public announcement you make on Keybase is now verifiably signed by Keybase and hashed into the Stellar blockchain. To be specific, all of these:

announcing your Keybase username
adding a public key
identity proofs (twitter, github, your website, etc.)
public bitcoin address announcements
public follower statements
revocations!
team operations
Quick background
Earlier, in the server security overview we described Keybase's approach to server security: (1) each user has his or her own signature chain that grows monotonically with each announcement; (2) the server maintains a global Merkle Tree that covers all signature chains; and (3) the server signs and publishes the root of the Merkle Tree with every new user signature. This configuration strongly discourages the server from lying by omission, since clients have the tools to catch the server in the act.

There was one point we glossed over. A sophisticated adversary Eve could commandeer our server and fork it, showing Alice and Bob different versions of server state. Eve could get away with her attack as long as she never tries to merge Alice and Bob's views back together, and as long as they don't communicate out-of-band. (See Mazières and Shasha for a formal treatment of fork-consistency).

Enter the Stellar Blockchain
Thanks to Stellar, we are unforkable.

Since 20 Jan 2020, Keybase has been regularly pushing its Merkle Root into the Stellar blockchain, signed by the key GA72FQOMHYUCNEMZN7GY6OBWQTQEXYL43WPYCY2FE3T452USNQ7KSV6E. Now, Alice and Bob can consult the blockchain to find a recent root of the Keybase Merkle tree. Unless Eve can fork the Stellar blockchain, Alice and Bob will see the same value, and can catch Eve if she tries to fork Keybase.

Another way to think of this property is to turn it on its head. Whenever Alice uploads a signed announcement to the Keybase servers, she influences Keybase's Merkle Tree, which in turn influences the Stellar blockchain, which in turn Bob can observe. When Bob observes changes in the Stellar blockchain, he can work backwards to see Alice's change. There's little Eve can do to get in the way without being detected.

You Mean My Signatures affect a Major Cryptocurrency Blockchain?

# NCC Group Protocol Security Review for Keybase found here: 

https://keybase.io/docs-assets/blog/NCC_Group_Keybase_KB2018_Public_Report_2019-02-27_v1.3.pdf

High Risk Issues - 1
Medium Risk Issues - 3
Low Risk Issues - 1
