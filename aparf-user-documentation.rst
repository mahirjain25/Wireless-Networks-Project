++++++++++++++++++
APARF User Manual
++++++++++++++++++

This document describes the **Adaptive Power Auto Rate Fallback (APARF)**
algorithm, and acts as a user manual for its
implementation in ns3.

Algorithm Synopsis
==================

APARF is a link adaptation algorithm, that dynamically adjusts both
the transmit power level and the data rate based on the radio channel
conditions. It is compatible with the IEEE 802.11 wireless LAN standard.

The algorithm **relies solely on the link quality information available at the
transmitter.** This information is indicated by the reception or non-reception
of the acknowledgement (ACK) frames. Unlike several other algorithms, this
algorithm **does not make use of the RTS-CTS**
(Request To Send - Clear To Send) mechanism, and **neither does it pass on
link information from the receiver to the transmitter.**

APARF can be operated in several modes, depending on situational factors such as:
    * The service requirements (Target data rate, delay constraints, etc.)
    * Number of stations in the network
    * The scenarios being considered (Fairness constraints)

The 3 modes of operation are as follows:
    * **High Performance Mode** - The system tries to drive up the data rates to minimise transmission delays and maximise throughput.
    * **Low Power Mode** - The goal is to reduce transmit power level, by sacrificing the throughput gain.
    * **Single Parameter Adaptation** - Either the transmit power level or the data rate is kept fixed.


