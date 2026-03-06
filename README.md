# AC Usage Detection and Isolation from Aggregated Electrical Current Data

## Overview

This project analyzes one day of aggregated electrical data to identify and isolate Air Conditioner (AC) usage from the total room current signal. The dataset contains timestamped voltage and current readings representing combined electrical consumption from multiple appliances such as lights, fans, and air conditioners.

The goal is to study patterns in electrical consumption and develop a logical method to distinguish AC operation from other appliance loads.

---

## Dataset

The dataset consists of the following columns:

| Column           | Description                                                        |
| ---------------- | ------------------------------------------------------------------ |
| device_timestamp | Unix timestamp (milliseconds) representing the time of measurement |
| voltage          | Voltage reading (Volts)                                            |
| current          | Total electrical current consumed in the room (Amperes)            |

From these values, additional features were derived such as power consumption, hourly usage patterns, and AC load estimates.

---

## Methodology

### 1. Data Preprocessing

* Converted `device_timestamp` into human-readable datetime format.
* Sorted the data chronologically to create a time-series signal.
* Calculated electrical power using:

Power (W) = Voltage × Current

---

### 2. Baseline Load Estimation

The background load represents appliances that run continuously such as lights, fans, and standby electronics.

To estimate this baseline:

* AC operation periods were first detected using a current threshold.
* Median current from **non-AC periods** was used as the background baseline.

This provides an estimate of typical non-AC electricity usage.

---

### 3. AC Detection

AC operation was identified using a threshold-based method:

```
AC_THRESHOLD = 5 Amperes
```

Readings above this value were classified as potential AC activity.

This works because:

* Lights and fans typically draw **< 0.5 A**
* AC compressors draw **5–20 A depending on capacity**

---

### 4. AC Load Isolation

Once baseline consumption was estimated, the AC signal was isolated using:

```
AC Current = Total Current − Background Baseline
```

Negative values were clipped to zero to ensure only positive AC consumption remained.

---

### 5. Behavioral Analysis

Additional analysis was performed to understand usage patterns:

* **Hourly AC usage patterns**
* **High current spikes detection**
* **AC compressor startup characteristics**
* **Power consumption statistics**

These analyses help distinguish AC activity from other appliances.

---

## Results

Key observations from the dataset:

* Most readings show **very low current (<0.3 A)** indicating background loads.
* High-current spikes correspond to heavy appliances.
* AC operation appears as **sustained high current segments**.
* Startup spikes and variability patterns suggest the AC behavior is consistent with an **inverter AC with soft start characteristics**.

Estimated AC size based on average power consumption:

**~1500–2000 W (approx. 1.5–2 Ton AC)**

---

## Output

The final processed dataset includes:

| Column        | Description                     |
| ------------- | ------------------------------- |
| timestamp     | Time of measurement             |
| voltage       | Voltage reading                 |
| current       | Total current                   |
| power_W       | Total electrical power          |
| is_ac         | AC detection flag               |
| load_type     | AC or background classification |
| bg_baseline_A | Estimated background current    |
| ac_isolated_A | Estimated AC current            |
| ac_isolated_W | Estimated AC power              |
| hour          | Hour of day                     |

The output file generated:

```
ac_isolated_output.csv
```

This file contains the final isolated AC signal along with supporting features.

---

## Key Insights

* Aggregated electrical signals can be decomposed into appliance-level patterns using simple signal analysis.
* AC usage can be identified using:

  * current magnitude
