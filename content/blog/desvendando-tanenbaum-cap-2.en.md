---
title: "Unveiling Tanenbaum: Chapter 2 – Signals, Media, and Data Transmission"
linkTitle: "Unveiling Tanenbaum: Ch. 2"
date: 2026-11-01T13:00:00-03:00
draft: false
series: ["Desvendando Tanenbaum"]
tags:
  - computer-networks
  - tanenbaum
  - physical-layer
  - data-transmission
  - optical-fiber
  - digital-modulation
  - multiplexing
summary: "An educational exploration of the physical layer, communication channel limits, and the technologies that carry bits across copper, optical fiber, and electromagnetic waves."
---

> **Academic Disclaimer and Credits:** This article is an educational and technical synthesis based on Chapter 2 ("The Physical Layer") of *Computer Networks* (6th Edition), by Andrew S. Tanenbaum, Nick Feamster, and David J. Wetherall. The text organizes and explains the concepts with original explanations, without replacing the reading of the original work.

## 1. Bits Do Not Traverse Cables

When we send a message, watch a video, or open a webpage, we often picture data traveling across the network as a sequence of zeroes and ones. This representation is convenient for software, but it does not describe what actually traverses the physical medium.

Cables do not transport abstract numbers. An optical fiber guides pulses of light. A copper pair experiences voltage variations. An antenna radiates electromagnetic waves. In all these cases, the receiver must observe a physical phenomenon and infer which symbols were transmitted.

This is the fundamental responsibility of the physical layer: establishing how bits are represented, transmitted, and recovered across a real medium. The problem seems simple until we realize that no channel is perfect. Signals attenuate, suffer distortion, pick up noise and interference, and compete for bandwidth with other transmissions.

The core thesis of this chapter stems from this constraint: **the speed of a network does not depend solely on the processor or the protocol, but on the physical properties of the channel and the techniques used to exploit its capacity**.

## 2. Every Complex Signal Is Composed of Simpler Components

A square pulse might appear to be an elementary way to represent a bit. In practice, it results from the superposition of many frequencies. Fourier analysis allows any periodic function to be decomposed into a sum of sines and cosines:

$$
g(t) = \frac{1}{2}c + \sum_{n=1}^{\infty} a_n \sin(2\pi nft) + \sum_{n=1}^{\infty} b_n \cos(2\pi nft)
$$

In this expression, $f = 1/T$ is the fundamental frequency of the periodic signal, while $a_n$ and $b_n$ represent the amplitudes of its harmonics.

The key takeaway for networking is not merely memorizing the mathematical formula. It lies in understanding that a physical channel does not transmit all these frequencies uniformly. Higher-frequency components typically suffer greater attenuation. When higher harmonics vanish, the received pulse loses its sharp edges and becomes significantly harder to distinguish.

```text
Ideal Signal                After the Channel

+-----+     +-----+             /\        /\
|     |     |     |            /  \      /  \
+     +-----+     +      ->   /    \____/    \
```

The **analog bandwidth** describes the range of frequencies that the channel can transmit without excessive attenuation. It is measured in Hertz (Hz). This must not be confused with the data rate, which is measured in bits per second (bps). One quantity describes available frequency spectrum; the other describes the amount of digital information transmitted per unit of time.

> **Example from the source:** The chapter analyzes the transmission of the ASCII character `b`, represented by `01100010`, across a telephone line with limited bandwidth. As the data rate increases, fewer harmonics can successfully traverse the channel, making the received waveform progressively harder to recognize.

(Tanenbaum, Feamster, and Wetherall, 6th ed., Section 2.4, pp. 69–73)

### Nyquist Proves That Even an Ideal Channel Has a Limit

Even in the absence of noise, a channel with finite bandwidth cannot support arbitrarily rapid signal variations. For a noiseless low-pass channel of bandwidth $B$ operating with $V$ discrete levels, the Nyquist limit is:

$$
\text{Maximum Data Rate} = 2B\log_2 V
$$

Here, $B$ is measured in Hertz and $V$ denotes the number of distinct signal levels used to represent symbols. By increasing $V$, each symbol carries more bits. However, this gain comes at a cost: levels that are spaced closer together become substantially harder to resolve reliably in the presence of noise.

### Shannon Accounts for Real-World Noise

Real channels are always subject to thermal and environmental noise. Shannon related bandwidth $B$ and the signal-to-noise ratio (the ratio of signal power $S$ to noise power $N$):

$$
C = B\log_2\left(1 + \frac{S}{N}\right)
$$

The channel capacity $C$ is expressed in bits per second. The signal-to-noise ratio is commonly expressed in decibels (dB):

$$
\operatorname{SNR}_{dB} = 10\log_{10}\left(\frac{S}{N}\right)
$$

Nyquist and Shannon answer two distinct fundamental questions. Nyquist demonstrates the impact of bandwidth and discrete signaling levels on an ideal noiseless channel. Shannon establishes the theoretical upper bound for a noisy channel. Neither formula automatically yields the real-world operational speed of an actual network, as practical implementations still contend with encoding overhead, interference, bit errors, and hardware imperfections.

## 3. Copper and Fiber Solve the Same Problem Differently

A guided medium directs signals along a physical transmission path. Twisted pair, coaxial cable, and optical fiber all belong to this category, yet their physical properties differ markedly.

### Twisting Copper Wires Is Not Purely Decorative

Twisted pair cabling uses two insulated copper conductors twisted around each other in a helical pattern. The receiver detects the potential difference (voltage) between them. Because external electromagnetic interference tends to affect both wires almost identically, common-mode noise can be effectively canceled at the receiver.

```text
Wire A   /\/\/\/\/\/\/\
Wire B   \/\/\/\/\/\/\/
```

Different cable categories (e.g., Cat 5e, Cat 6, Cat 6A, Cat 8) specify increasingly strict construction tolerances, higher operating frequencies, and improved shielding. The category rating stamped on a cable jacket is not an isolated guarantee of data rate: transmission distance, connector termination quality, installation practices, and external interference also determine the overall link performance.

Coaxial cable arranges conductors concentrically: a central copper core, an insulating dielectric layer, and an outer conductive braid or foil shield. This geometry provides superior electromagnetic shielding, making coaxial cable historically prominent in cable television systems and HFC (*Hybrid Fiber-Coaxial*) broadband networks.

### In Optical Fiber, Information Travels as Light

An optical fiber consists of a high-refractive-index glass core surrounded by a cladding with a lower refractive index. Under appropriate incidence angles, light remains confined within the core via total internal reflection.

```text
Cladding
================================================
   \      /\      /\      /\      /
----\----/--\----/--\----/--\----/---- Core
     \  /    \  /    \  /    \  /
================================================
Cladding
```

In multimode fiber, light rays can propagate along multiple geometric paths (modes), arriving at slightly different times and causing modal dispersion. In single-mode fiber, the core diameter is small enough (typically 8 to 10 microns) that light propagates along a single mode, virtually eliminating modal dispersion and enabling much longer distances at significantly higher data rates.

Optical transmission windows are also critical because silica glass attenuation varies as a function of optical wavelength. Selecting light sources (LEDs vs. semiconductor lasers), photodetectors, and operating wavelengths involves an engineering trade-off among cost, reach, and throughput.

| Criterion | Twisted Pair | Coaxial Cable | Optical Fiber |
|---|---|---|---|
| Signal Type | Electrical | Electrical | Optical |
| Electromagnetic Interference | Susceptible | Low due to outer shield | Completely immune |
| Installation | Simple and inexpensive | Stiffer, moderate complexity | Requires specialized splicing & precision |
| Typical Reach | Short (up to 100 m) | Short to metropolitan | Long (tens to thousands of km) |
| Potential Capacity | Moderate | High | Extremely high |

Fiber is not universally superior in every scenario: while it delivers unparalleled bandwidth and minimal attenuation, optical transceivers, fusion splicing, and maintenance require specialized tools, rigorous handling, and higher initial capital expenditure.

(Tanenbaum, Feamster, and Wetherall, 6th ed., Section 2.1, pp. 57–64)

## 4. Wireless Is a Shared, Unpredictable Medium

Inside a cable, signals remain relatively well-confined. In wireless communication, radiated energy disperses into the environment, encounters obstacles, and arrives at the receiver via multiple indirect trajectories.

Frequency and wavelength are governed by the fundamental relationship:

$$
\lambda f = c
$$

Where $\lambda$ is the wavelength, $f$ is the frequency, and $c$ is the speed of light in the propagation medium. Different radio spectrum bands exhibit wildly different propagation behaviors, demanding tailored antenna designs, transmission power levels, and modulation schemes.

Low-frequency radio waves can diffract around obstacles and follow the curvature of the Earth as ground waves. Microwave transmissions generally require line-of-sight propagation and enable tightly focused directional beams. Infrared and visible light provide highly localized communications, but are easily blocked by opaque physical barriers.

### The Same Signal Can Arrive Multiple Times

In indoor and dense urban environments, radio waves reflect off walls, terrain, and buildings. The receiving antenna picks up multiple delayed, phase-shifted copies of the same transmitted signal. Depending on their relative phases, these multipath arrivals can constructively reinforce or destructively cancel each other out. This phenomenon is known as **multipath fading**.

```text
Transmitter ------ direct path -------> Receiver
     \                                  /
      \---- reflection off obstacle ---/
```

Signal power also drops with distance. In free space, power density disperses across an expanding spherical surface following an inverse-square law (or worse in cluttered environments). Consequently, link distance, frequency selection, physical obstacles, and transmission power must be evaluated together.

### Spreading the Spectrum Increases Resilience

In Frequency Hopping Spread Spectrum (FHSS), the transmitter and receiver rapidly switch frequencies in synchrony according to a pseudo-random code sequence. Narrowband interference might disrupt a few frequency hops, but will not interrupt the entire communication link.

In Direct Sequence Spread Spectrum (DSSS), each data bit is combined via XOR with a higher-rate pseudo-random sequence of *chips*, spreading the signal energy across a much wider frequency band. This technique enables the receiver to extract the intended signal even below the background noise floor.

Ultra-Wideband (UWB) uses very short pulses distributed across an extensive frequency band. Its fine temporal resolution makes it exceptionally well-suited for high-precision indoor localization and ranging applications.

(Tanenbaum, Feamster, and Wetherall, 6th ed., Sections 2.2 and 2.3, pp. 64–69)

## 5. Modulation: The Art of Encoding Bits into Physical Signals

The physical layer must choose which observable physical properties to alter in order to carry information: voltage level, amplitude, frequency, phase, or a multidimensional combination thereof.

### Baseband Transmission Uses Direct Signal Levels

In baseband transmission, the signal occupies frequencies starting near zero Hertz. Line codes dictate how binary bitstreams translate into electrical levels and transitions.

In Non-Return-to-Zero (NRZ), distinct voltage levels represent binary zeroes and ones. While conceptually straightforward, long sequences of identical bits produce constant DC voltage with no transitions, making receiver clock recovery notoriously difficult.

In Non-Return-to-Zero Inverted (NRZI), transitions (or the lack thereof) represent binary values. Block codes such as 4B/5B substitute groups of 4 data bits with preselected 5-bit code words to guarantee a sufficient density of signal transitions for robust clock synchronization.

The engineering challenge extends far beyond merely representing zeroes and ones: an effective line code must balance clock synchronization, spectral efficiency, DC baseline drift, and error detection capabilities.

### Passband Modulation Modifies a Carrier Wave

When a channel operates across a specific radio or passband frequency spectrum, digital data modulates a high-frequency carrier wave. Amplitude Shift Keying (ASK) modulates amplitude, Frequency Shift Keying (FSK) modulates frequency, and Phase Shift Keying (PSK) modulates carrier phase.

In Quadrature Phase Shift Keying (QPSK), four discrete phase shifts represent four distinct symbols, encoding two bits per symbol. Quadrature Amplitude Modulation (QAM) simultaneously modulates both amplitude and phase. Denser constellation diagrams carry more bits per symbol, but require a higher signal-to-noise ratio so the receiver can accurately resolve adjacent constellation points.

```text
Q
^       *       *
|
|
+-----------------> I
|
|       *       *
```

The symbol rate, measured in baud, denotes how many distinct physical symbols are transmitted per second. If each symbol encodes $k$ bits, the resulting data rate is:

$$
\text{bit rate} = \text{symbol rate} \times k
$$

Increasing the number of bits per symbol elevates spectral efficiency, but diminishes the noise margin. Modern wireless and wired modems dynamically adjust their modulation schemes according to real-time channel conditions.

### Multiplexing Allows Multiple Data Streams to Share a Single Medium

Multiplexing enables independent communication flows to share expensive physical infrastructure:

- **FDM** separates users across distinct frequency sub-bands;
- **TDM** allocates dedicated, repeating time slots to each user;
- **WDM** multiplexes optical signals across distinct wavelengths (colors) of light within a single fiber;
- **OFDM** divides a wide channel into multiple closely spaced, orthogonal narrowband subcarriers.

| Technique | Shared Resource | Core Mechanism |
|---|---|---|
| FDM | Frequency | Each communication flow occupies a dedicated frequency slice |
| TDM | Time | Each user is allocated periodic, exclusive time slots |
| WDM | Optical Wavelength | Multiple wavelengths of light concurrently share one fiber core |
| OFDM | Orthogonal Subcarriers | Data is split across orthogonal parallel narrowband subcarriers |

In Code Division Multiple Access (CDMA), multiple transmitters concurrently share both frequency and time by modulating their data with distinct chip sequences. If these chip sequences are mathematically orthogonal, the receiver can compute the cross-correlation of the composite signal with the desired code, extracting a single station's transmission while treating all others as low-level background noise.

For two length-$m$ chip sequences $S$ and $T$, orthogonality is defined as:

$$
S \cdot T = \frac{1}{m}\sum_{i=1}^{m} S_iT_i = 0
$$

A dot product of zero indicates zero mutual cross-correlation. In operational commercial systems, multipath propagation, dynamic power control, and timing synchronization make CDMA engineering substantially more demanding than the idealized mathematical formulation.

(Tanenbaum, Feamster, and Wetherall, 6th ed., Section 2.4.4, pp. 78–83)

## 6. The Last Mile Reveals Engineering Trade-offs

The theoretical techniques described above converge within the access networks connecting homes, businesses, and mobile endpoints. Every access architecture represents a pragmatic compromise designed to reuse legacy infrastructure or circumvent physical bottlenecks.

### Telephony Reused Legacy Copper Wires

The Public Switched Telephone Network (PSTN) was originally engineered exclusively for 4 kHz analog voice circuits. The local loop connecting customer premises to the central office consisted of unshielded twisted pair copper. Digital Subscriber Line (xDSL) technologies repurposed the higher, unused frequency spectrum on those same copper lines to transmit digital data.

In Asymmetric DSL (ADSL), the available bandwidth is partitioned into discrete subchannels, allocating substantially more capacity to the downlink than the uplink. This asymmetry reflected traditional residential internet usage patterns, which heavily prioritize downloading over uploading.

In modern Fiber To The Home (FTTH) deployments, Passive Optical Networks (PON) dominate the last mile. A passive optical splitter divides a single feeder fiber among dozens of subscribers without requiring active electronic components or power supplies in the field.

```text
OLT ---- fiber ---- passive splitter ---- ONT 1
                           |------------- ONT 2
                           +------------- ONT 3
```

### Cable Television Evolved into a Data Highway

Hybrid Fiber-Coaxial (HFC) networks integrate optical fiber backbones with neighborhood coaxial distribution drops. The DOCSIS (*Data Over Cable Service Interface Specification*) standard governs how cable modems communicate across this shared medium. Downstream traffic is broadcast across the coaxial branch; in the upstream direction, central equipment must orchestrate subscriber transmissions using time slots to prevent packet collisions.

Unlike dedicated telephone copper pairs, subscribers on an HFC coaxial segment share bandwidth. Realized throughput is inherently contingent upon network provisioning, neighborhood density, and concurrent subscriber utilization.

### Mobile Connectivity Relies on Cellular Reuse and Handover

Cellular networks partition physical geography into discrete radio cells. Because signal power attenuates with distance, frequencies can be safely reused across non-adjacent cells without generating destructive co-channel interference, substantially expanding total network capacity. As a mobile station moves across cell boundaries, the network seamlessly coordinates a **handover** (or handoff) to maintain an uninterrupted session.

Cellular network generations have radically transformed mobile voice, data rates, and core architecture: early systems were analog and circuit-switched; 3G introduced packet data; 4G LTE migrated to an all-IP flat core; and 5G NR leverages massive MIMO beamforming, millimeter-wave spectrum, and software-defined network slicing.

Satellites offer complementary global coverage: Geostationary (GEO) satellites maintain a fixed position relative to the surface of the Earth, but their 35,786 km altitude imposes an unavoidable ~250 ms one-way propagation delay. Low Earth Orbit (LEO) constellations (such as Starlink) orbit at 500–1,200 km, dramatically cutting propagation latency down to tens of milliseconds, at the expense of requiring large orbital constellations and continuous inter-satellite handovers.

(Tanenbaum, Feamster, and Wetherall, 6th ed., Sections 2.5 to 2.8, pp. 83–118)

## 7. Technical Glossary: The Physical Layer

| # | Technical Term | Concise Explanation | Rigorous Technical Definition |
|---|---|---|---|
| 1 | Bandwidth | Available frequency range | The range of continuous frequencies transmitted across a channel without severe attenuation |
| 2 | Attenuation | Signal loss over distance | Reduction in signal power/amplitude as electromagnetic energy propagates through a medium |
| 3 | Harmonic | Sine wave component | A sinusoidal frequency component that is an integer multiple of the fundamental frequency |
| 4 | Baud | Symbol transmission rate | The number of signal state transitions or physical symbols transmitted per second |
| 5 | QAM | Amplitude and phase modulation | A modulation scheme encoding digital bits into discrete points across an in-phase/quadrature constellation |
| 6 | OFDM | Parallel orthogonal subcarriers | A digital multicarrier modulation scheme distributing data over orthogonal, closely spaced subcarriers |
| 7 | Total Internal Reflection | Light guidance in optical fiber | Optical phenomenon confining light rays within a denser core when incident beyond the critical angle |
| 8 | WDM | Multiple wavelengths per fiber | Optical frequency multiplexing sending distinct data streams simultaneously over different optical wavelengths |
| 9 | CDMA | Multiple access via unique codes | Spread spectrum technique separating concurrent transmissions using orthogonal pseudorandom chip sequences |
| 10 | HFC | Combined fiber and coaxial plant | Hybrid broadband access architecture combining optical fiber backbones with neighborhood coaxial cables |
| 11 | PON | Passive point-to-multipoint fiber | Optical access network distributing bandwidth to endpoints via unpowered optical splitters |
| 12 | Handover | Seamless cellular transfer | The automated procedure transferring an active mobile session from one base station/channel to another |

## 8. Physics Delivers Bits, Not Meaning

The physical layer establishes the immutable physical foundation upon which every subsequent layer of the networking stack operates. Fourier analysis reveals the harmonic composition of periodic signals. Nyquist dictates the theoretical upper bound on signaling speed in an ideal noiseless channel. Shannon defines the absolute mathematical capacity ceiling in the presence of noise.

Copper, fiber optics, and wireless spectrum are not merely interchangeable pipes: each imposes distinct trade-offs regarding manufacturing cost, signal attenuation, propagation distance, and susceptibility to environmental interference. Digital modulation and multiplexing translate these physical constraints into engineering solutions: determining how many bits can be squeezed into each symbol, how multiple users can share a transmission medium, and how much noise a receiver can tolerate.

Yet, successfully receiving a stream of bits does not ensure receiving a coherent, meaningful message. The physical layer cannot determine where a message begins or ends, cannot verify whether received bits contain undetected errors, and cannot prevent a fast transmitter from overflowing a slow receiver. Those fundamental problems belong to the layer directly above: the **Data Link Layer**.

> **Study Conclusion:** The physical layer translates abstract digital information into measurable physical phenomena and governs the fundamental limits of communication speed, distance, and reliability. Understanding signals, media, modulation, and multiplexing lays the groundwork for examining the Data Link Layer, which structures raw bitstreams into frames, manages link-level error detection, and coordinates shared access to physical channels.
