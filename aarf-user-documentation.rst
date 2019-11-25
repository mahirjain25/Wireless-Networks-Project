++++++++++++++++++
AARF User Manual
++++++++++++++++++

This document describes the **Adaptive Auto Rate Fallback (AARF)**
algorithm, and acts as a user manual for its
implementation in ns3.

Algorithm Synopsis
==================

ARF is good at handling short-term variations but fails when it comes to handling stable conditions. To overcome this drawback in ARF, AARF was proposed, with the difference being the threshold i.e, the number of consecutive successful transmissions required to change to a higher data rate not being fixed. It is adapted using **Binary Exponential Backoff**. Its value can be any of the following: 10, 20, 40, 60. 

The algorithm works in the following way. If the first packet transmitted at a new data rate is a failure, the value of the threshold is doubled and the sender falls back to the previous rate. In case, the first packet transmitted at the new data rate is a success, the threshold is reset to 10 and the transmission continues. 

If there are two consecutive failures, the threshold is reset to 10 and the sender goes to the data rate which is lower than the current date rate. If the sender is already at the lowest data rate, the threshold is not reset.  

Design
==================
