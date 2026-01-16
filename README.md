# Two-Way-OFDM-AF-Relay
## 📌 Project Overview
This repository contains a comprehensive MATLAB simulation of a Two-Way Relaying Network (TWRN) utilizing Orthogonal Frequency Division Multiplexing (OFDM) and Amplify-and-Forward (AF) protocols. The project performs a comparative analysis between Fixed Gain (FG) and Variable Gain (VG) amplification strategies, evaluating system reliability and accuracy through Bit Error Rate (BER), Outage Probability, and Constellation analysis.
## 🚀 Key Features
### 1)Two-Way Relaying Logic:
Implements the Multiple Access (MAC) phase and Broadcast (BC) phase, allowing two nodes to exchange data simultaneously via a relay.
### 2)OFDM Implementation:
Includes IFFT/FFT operations and Cyclic Prefix (CP) insertion to mitigate Inter-Symbol Interference (ISI) in a Rayleigh fading environment.
### 3)Self-Interference Cancellation:
Features advanced receiver logic where nodes subtract their own known transmitted signals from the relay's broadcast to extract desired data.
### 4)Dual Gain Comparison:
Fixed Gain (FG): Employs a constant amplification factor regardless of channel fluctuations.
Variable Gain (VG): Implements adaptive amplification based on instantaneous Channel State Information (CSI).
### 5)Performance Metrics: Detailed Monte Carlo simulations to plot BER vs. SNR and Outage Probability vs. SNR.
##📊 Technical Analysis:
The simulation demonstrates that Variable Gain significantly outperforms Fixed Gain in dynamic wireless environments:
### 1)Bit Error Rate (BER):
VG achieves a steeper "waterfall" curve by optimizing power allocation during deep fades.
### 2)Outage Probability:
The system shows higher availability (lower outage) under VG due to its ability to maintain a target capacity threshold of $1.0 \, \text{bps/Hz}$.
### 3)Constellation Fidelity:
Scatter plots reveal tighter QPSK clusters for VG, whereas FG exhibits scattered "noise clouds" due to its inability to adapt to instantaneous Rayleigh fading.
## 🛠️ Requirements:
MATLAB (R2018b or later recommended)Communications Toolbox
## 📂 Project Structure:
two_way_ofdm_relay.m: Main simulation script containing the multi-time loop, channel modeling, and visualization.results/: Directory containing performance graphs (BER, Outage, Constellations).
## 💡 Use Case:
This project is highly relevant for research in 5G/6G Backhaul systems, Satellite Communications, and Cooperative Diversity frameworks. It serves as a practical implementation of the theoretical concepts found in standard wireless communication R&D workflows.
