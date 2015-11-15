# Primer
What is Object Storage? Let's start with a mouthful, and break it down. Object storage is an globally addressable, immutable key value store, meant for secondary storage.
Yikes - that's like $300 of words in one sentence.
"I'm a dev" you say, "I grok this." Cool - skip to the next section. Otherwise, let's keep going.
Objects - blobs of data - are things you want to store. Backup data. Web assets. Save files for your cloud based game. Object storage allows you to address them globally. This is really important. 
## Addressability
The "addressibility" of your object is universal - that is, you could be inside your corporate network on a VPN, or you could be hooked up through a satellite link from your straw hut in polynesia on the public internet. It's the same URL. I want to underscore the importance of this - a whole class of architectural problems in the cloud stem from not really thought out addressability. A URL must be universal - I mean the 'U' is right there - but it could be lying to you. If you can access it *anywhere*, using the same label - the URL - then it's a real URL. Object storage relies heavily on correct DNS and accomplishes object addressability with 2 key concepts - the ***bucket***, and the ***key***. There are exactly 2 ways to build a URL. *Path style* and *Host style*. 

###### Examples:
* Host: http://**bucket**.os.ctl.io/**key**
* Path: http://os.ctl.io/**bucket**/**key**

## Immutable Key Value Store
### im·mu·ta·ble
> adjective
> 
> unchanging over time or unable to be changed.

Buckets are mutable. I'm talking about the object themselves - they are *not* mutable. When you interact with object storage, you assert that something exists, or it doesn't exist, in a bucket. Consider three users: Alice, Bob, and Charlie. Let's call him Chuck, I think he'd be ok with that. Also, Alice and Bob both have a weird preoccupation with security, but that's not important here :)
#### Alice
Alice has a web site. She has several thousand assets - css, images, a couple PDF's for sales.
Bob is an admin. He's responsible for an Exchange server and a few databases. 
In this case, Alice's needs are straightforward. She could rely on object storage to save her assets. They'll work nicely because she can set readonly world access on URL's which she can simply drop in place in her site.
#### Bob
Bob should consider his use case. He's got potentially large files that change slightly over time. If he took the naive approach and simply copied the files over to his backup bucket nightly, he's wasting quite a bit of effort because he must upload the entirety of all his databases every night. There is software out there that mitigates this problem by splitting large binaries into chunks, minimizing the overhead of writing something entirely or not at all.
#### Charlie
"Chuck" has relational databases in his blood. He lives in a land of queries and transactions. He's currently working as a contractor for a real estate company, and he's throwing together the backend for listings. He sees the data is somewhat document like - they have descriptions, but what draws him to an object store is they are heavy on blobs of data - weird angles of the bedrooms, a movie showing the indoor pool in one listing, etc. He needs to be thoughtful about how to pull this off. On one hand, he could store related assets with a document using a prefix, and search. Ahh, queries. Another design would be store an object that references the other objects, creating a tree. The first idea is actually pretty bad. Some of this is key-value store 101, sure, but it's especially important with services like object storage which emphasize data durability and cost over performace. Queries are slow - you should build a design where you know what your keys are in advance.
## Secondary Storage
Let's talk about your computer for a second. You have a CPU with a cache. Really fast memory, very small, very expensive. Next, you have RAM. Pretty fast (but nothing compares to a CPU cache!), a lot more space, not as expensive. Next, you've got bulk storage. Your disk. Big, much slower, cheap.
Object storage is the hard drive of your cloud solution. We keep the data safe by storing redundancy along with your data. We don't charge much for it. It's speed will also never compete with a local KV store like Redis. If we go back to Alice's example above, it's possible that she may want to implement a cache of some kind on top of object storage. She may not need to, but it's likely a consideration. The good news for her is that S3 is built on top of HTTP, so out of the box solutions like Varnish or NGINX work quite well.
Any object storage offering you find, consider latency and bandwidth will vary considerably over time.