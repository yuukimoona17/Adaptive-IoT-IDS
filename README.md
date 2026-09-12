# Adaptive IoT Network Intrusion Detection: Overcoming Domain Shift via Hybrid Architectures

## Overview
Deep learning models for Intrusion Detection Systems (IDS) often memorize specific hardware communication frequencies and packet sizes instead of actual threats. This causes the models to collapse when deployed to a new device without updates, known as a Zero-Update deployment. This project develops a Hybrid Architecture utilizing Few-Shot Domain Adaptation to learn flow behaviors with minimal computational overhead.

## Data Preprocessing
The preprocessing pipeline forces the model to focus on behavioral learning through three stages:
*   **Stage 1:** Hardware fingerprints, including IP, MAC, Timestamp, and Port, are removed to isolate pure flow behaviors.
*   **Stage 2:** Numeric features such as frame.len and ip.ttl are extracted and standardized using MinMaxScaler.
*   **Stage 3:** Sequential time-series data is created for temporal analysis using a sliding window of 10.

## Model Architecture
The project replaces the benchmark LSTM baseline (which uses 2x LSTM hidden layers) with a proposed Hybrid CNN-GRU-Attention network:
*   **CNN-1D:** Extracts spatial features and local relations across the 10-packet window.
*   **GRU:** Processes temporal features using fewer parameters than LSTM, allowing for faster inference.
*   **Attention Mechanism:** Automatically weights crucial malicious packets while filtering out benign noise.
*   **Fully Connected:** Performs the final binary classification (0: Benign, 1: Attack).

## The Cross-Device Collapse
Both architectures demonstrate exceptional capability when evaluated on familiar source hardware (Air Quality Sensor), with the Hybrid model reaching 97.17% accuracy, an Attack F1-Score of 0.9766, and a Benign F1-Score of 0.9640. However, testing on approximately 5.5 million new IP Camera packets proved that these models cannot generalize across different IoT devices without adaptation. During a Zero-Update deployment, the Hybrid model's accuracy plummeted to 26.52%, while the LSTM baseline plummeted to 24.27% with a Benign F1 score of 0.0034.

## Stratified Few-Shot Fine-Tuning
To overcome the domain shift, the project introduces a high-efficiency adaptation strategy:
*   **Overcoming Temporal Label Imbalance:** Naive sampling of the first 10% of packets blinds the model to attack patterns, causing the Attack F1 score to drop to 0.0058%. The system fixes this by using Stratified Sampling to distribute the 10% sample evenly and capture exact threat ratios.
*   **Architecture Adaptation:** The CNN-1D and GRU layers are frozen to preserve domain-invariant feature extraction. The Attention and Fully Connected layers are unfrozen to rapidly adapt to the target distribution in 5 Epochs using only the 10% stratified data.

## Final Performance & Contributions
The system pioneered a Stratified Few-Shot approach that minimizes computational cost while achieving greater than 99% accuracy on novel devices.
*   **Target Domain Accuracy:** 99.58% evaluated on approximately 5 million test packets.
*   **F1-Scores:** 0.9970 for Benign traffic and 0.9930 for Attack traffic.
*   **Inference Latency:** 11.5ms per batch.