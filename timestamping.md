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

# Performance

We've made no performance optimizations yet. There's a lot of low-hanging fruit.

# NCC Group Protocol Security Review for Keybase found here: 

https://keybase.io/docs-assets/blog/NCC_Group_Keybase_KB2018_Public_Report_2019-02-27_v1.3.pdf

High Risk Issues - 1
Medium Risk Issues - 3
Low Risk Issues - 1
