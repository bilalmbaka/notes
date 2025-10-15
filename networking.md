# CHAPTER 1

# WIFI

## WPA KEY HEIRARCHY

- [network lessons website](https://networklessons.com/wireless/introduction-to-wpa-key-hierarchy)

The 802.11i describes what we commonly know as Wi-Fi Protected Access (WPA).
The Wi-Fi Alliance created WPA, which is based on the 802.11i standard.

Instead of using a single key for everything, WPA uses a hierarchy
with many keys to encrypt and check the integrity of different 802.11 frames.

WPA version one uses TKIP, WPA version two can use TKIP and/or AES-CCMP and WPA3 uses
AES-CCMP and GCMP-256.

In wireless 802.11 terminology, a wireless client is officially called a station (STA).

### Pairwise and group keys

For unicast traffic, Pairwise keys is used because there is a pair of keys between each wireless client and the AP. The AP has multiple pairwise keys, one for each associated wireless client.

broadcast and multicast traffic. All wireless clients should be able to encrypt and decrypt
this traffic All associated wireless clients of the AP have the same key. We call this a group key. The Pairwise and group keys are created differently.

### Pairwise master key

The pairwise master key (PMK) is a 256-bit key at the top of the key hierarchy and is used indirectly for unicast traffic and the WPA 4-way handshake. The wireless client and AP have the PMK, which should last the entire session, so it should not be exposed. To accomplish this, we use different keys derived from the PMK.

![pmk image](/networking_images/wpa-key-hierarchy-pmk.png)

There are two options to define this key:

- Pre-shared key
- AAA key

### AAA Key

A pre-shared key is not a secure option if you have many wireless clients because everyone uses the same pre-shared key. It’s also not scalable if you have a large enterprise wireless network. We can also use Authentication, Authorization, and Accounting (AAA) and 802.1X, also known as WPA-enterprise.

The AAA key is also called the Master Session Key (MSK).

When you use 802.1X, the authentication server (typically RADIUS) derives the PMK and exports it to the wireless client and AP. This is done using RADIUS attribute MS-MPPE-Recv-key (vendor_id=17). Each EAP method defines its own way of deriving the PMK.

The advantage of this solution is that each wireless client and AP use their own PMK, and it is a central solution. The downside is that setting this up is more complex than pre-shared keys.

### Pairwise Transient Key (PTK)

The Pairwise Transient Key (PTK) is used for encryption and integrity checks in unicast user data. It is also used for protecting the 4-way handshake.

The PTK is derived by combining these attributes:

- PMK
- AP Nonce (Anonce)
- STA Nonce (Snonce)
- AP MAC address
- STA MAC address

These attributes are inserted in a pseudo-random function (PRF); the output is the PTK.

As you can see, the PMK is part of the input. The nonces are randomly generated values. Something extra is needed for the computation because if not, you would end up with the same temporal keys every time. This is achieved by adding nonces to the computation. The wireless client creates the STA Nonce (Snonce) and the AP the AP Nonce (Anonce).

The client’s and AP’s identities are also bound in the computation by including their MAC addresses.

The PTK is transient because every time a client associates with the AP, a new PTK is derived. The PTK key size depends on whether you use TKIP or AES-CCMP.

The PTK is used to protect two items:

The WPA 4-way handshake
User data
For these two items, we require encryption and integrity.

The WPA 4-way handshake creates the keys on the wireless client and AP and has to be protected.

The user data is the data that we encapsulate in 802.11 frames. More specifically, this is the MAC Service Data Unit (MSDU). The MSDU is the payload we receive from the network layer (layer three), sent to the data link layer (layer two). The Logical Link Control (LLC) sub-layer adds an LLC header. In a nutshell, it’s an IP packet with an LLC header.

The PTK is partitioned into multiple keys, and TKIP and AES-CCMP use different parts of the key differently. Let’s dive into this.

### Group Master Key (GMK)

The group master key (GMK) is a 128-bit key at the top of the hierarchy for broadcast and multicast traffic. The AP generates a cryptographic-quality random number. The “cryptographic quality” part is important because some functions that generate random numbers are predictable. The GMK is not exchanged directly but is used to derive a group temporal key (GTK). The GMK can be regenerated periodically to reduce the risk of being compromised.

### Group Temporal Key (GTK)

The GTK is derived from the GMK using a PRF and protects all broadcast and multicast traffic between the AP and wireless clients associated with the AP. All wireless clients use the same GTK.

During the WPA 4-way handshake, the GTK is sent from the AP to the wireless client. When the AP renews the GTK, it uses a group key handshake called a 2-way handshake.

The GTK is partitioned into multiple keys and differs for TKIP and AES-CCMP.

## WPA and WPA2 4-Way Handshake
