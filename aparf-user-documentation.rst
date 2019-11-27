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
    * **High Performance Mode** - The system tries to drive up the data rates
      to minimise transmission delays and maximise throughput.
    * **Low Power Mode** - The goal is to reduce transmit power level, by
      sacrificing the throughput gain.
    * **Single Parameter Adaptation** - Either the transmit power level or
      the data rate is kept fixed.

**Key Assumptions made:**
    * Power consumed during idle times (DIFS, SIFS) not considered.
    * As the ACK procedure's reliability is crucial for the system, it is
      assumed that all ACK frames are transmitted at the highest power level.

Joint Power and Data Rate Adaptation:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As mentioned before, the ACK mechanism is used to detect a frame loss. If an
ACK for a frame is not received in a certain time frame, the algorithm
concludes that the link quality was insufficient and that a lower data rate
or higher transmission power should be utilised.

Conversely, if ACK is received for multiple frames successfully, the algorithm
concludes that the link quality is sufficient and that a higher data rate or
lower transmit power can be used.

Each node maintains 2 counters *s* and *f* to keep track of number of
successive transmission successes and failures respectively. Whenever either
is incremented, the other is set to 0. Further, thresholds S\ :sub:`max` and
F\ :sub:`max` are maintained. Whenever *s* reaches S\ :sub:`max`, the data
rate is increased or the power is increased and both counters are reset to 0.
Similarly, when *f* reaches F\ :sub:`max`, the data rate is decreased or
power is increased and both counters are reset to 0.

By default, F\ :sub:`max` = 1. The choice for S\ :sub:`max` depends on the
state of the channel. Fast changing channel dynamics call for a lower value
of S\ :sub:`max` and stable channel dynamics allow for a higher value.


The algorithm operates in 3 states:
    * High - When the doppler spread is high (fast-changing channel
      conditions), we use a lower S\ :sub:`max` value = S\ :sub:`1`
    * Low - When the doppler spread is low (stable channel
      conditions), we use a higher S\ :sub:`max` value = S\ :sub:`2`
    * Spread? - On reaching the success threshold, this state is entered,
      and the result of the next transmission is used to determine the
      state of the channel.  If the next transmission succeeds, the node
      assumes that the link quality is improving rapidly, enters state High,
      and sets S\ :sub:`max` to the small value S\ :sub:`1` to react quickly
      to the changing link quality. Else, if the next transmission fails, the
      link quality is assumed to either change slowly or not at all, so that
      the former decision to change the transmission parameter was premature,
      and accordingly state Low is entered.

High Performance Mode
~~~~~~~~~~~~~~~~~~~~~

On reaching the success threshold, the data rate is increased till the highest
possible rate, beyind which the transmit power levels are reduced.

In case of reaching the failire threshold, the transmit power is increased
to try and maintain the current data rate. The rate is only reduced if we
reach the maximum transmit power level.

Further, a variable called rate\ :sub:`crit`, representing critical rate is
used in the algorithm. It represents the lowest possible data rate that cannot
be supported even at the highest transmit power level.

Hence, on reaching the failure threshold F\ :sub:`max`, if the transmit power
is at the maximum level, rate\ :sub:`crit` is set to the current rate, and then
the data rate is reduced.

Similarly, on reaching the success threshold, S\ :sub:`max`, if the critical
rate is not set, then the rate is increased, else it makes more sense to reduce
the power. For this particular condition, the power is reduced a fixed number
of times beyond which the critical rate is reset, and the data rate is
increased.

Low Power Mode
~~~~~~~~~~~~~~

It is the dual of the High Performance Mode.
The dualities are as follows:

    * Notions of *rate* are replaced by *pow*
    * Increment operators are replaced by decrement operators.
    * Notions of *min* replaced by *max*

If a certain power level results in failed transmission at the lowest data
rate, then this power level is stored as power\ :sub:`crit`, and the power
level is increased. If the power level above this critical level results in
successful transmissions even after multiple increases of the data rate (
R\ :sub:`max`), then channel conditions are assumed to have improved, and
the algorithm tries to reduce the power to the critical level at the lowest
data rate.


Implementation Details
======================








