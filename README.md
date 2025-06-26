# 🔆 Zelestra Hackathon – Phase 2: Solar Loss Attribution

## 📌 Problem Statement

In a real-world solar PV plant, the theoretical energy output is rarely matched due to various loss factors. Your challenge is to **quantify and explain the gap between theoretical generation and actual energy output**.

You are provided with:
- Actual generation data (from SCBs, Inverters, PPC)
- Weather, irradiance, tracker, and module-level sensor data

### Your goal:
To model and **attribute losses** due to:
- ☁️ Cloud Cover
- 🕶️ Shading (Static/Dynamic)
- 🌡️ Temperature Effects
- 🌫️ Soiling
- ❓ Other Open-Ended or Unexplored Losses

---

## 📊 Deliverables

- A **report/dashboard** that includes:
  - ✅ Theoretical generation (calculated or modeled)
  - ✅ Actual vs estimated generation comparison
  - ✅ Boolean loss classification at 15-min intervals
  - ✅ Quantified losses at Plant, Inverter, and String level — on 15-min, hourly, daily, weekly, monthly basis
  - ✅ Visualizations: asset ranking, loss charts, string/inverter performance
  - ✅ Assumptions, methodology, and loss attribution logic

---

## 🧾 Sample Output Table

| datetime       | Zone   | Inverter | String | CloudCover | Shading | TempEffect | Soiling | OtherLoss1 | ... |
|----------------|--------|----------|--------|------------|---------|-------------|---------|-------------|-----|
| 01-10-2024 00:00 | CTIN03 | INV-03   | 8      | 0          | 0       | 0           | 0       | 1           |     |
| 01-10-2024 00:00 | CTIN08 | INV-08   | 9      | 0          | 0       | 0           | 1       | 0           |     |

---

## 🧭 Data Summary

### 📍 Plant Static Information

- **Location**: Latitude 38°0′2″N, Longitude 1°20′4″W  
- **Timezone**: UTC +1  
- **Capacity**: 45.6 MWh  

---

### ⚙️ Inverter Arrangement

| Inverter | Zone     | SCBs | Rated Power (kW) |
|----------|----------|------|------------------|
| INV_03   | EM3 Zone | 13   | 3800             |
| INV_08   | EM8 Zone | 12   | 3800             |

---

### 📋 Available Data Tags (Sensor Columns)

| **Category**                  | **Parameter**                                                               | **Tag(s) / Source Examples**                                                                                                                                  | **Unit**     |
|------------------------------|------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| Solar                        | Plane-of-array POA irradiance from Weather Stations EM3/EM8                 | `meteorolgicas_em_03_02_gii`                                                                                                                                  | W/m²         |
| Solar                        | Global horizontal irradiance (GHI)                                          | `meteorolgicas_em_03_02_ghi`                                                                                                                                  | W/m²         |
| Solar                        | Global Incident reflected irradiance (POA reflected)                        | `meteorologicas_em_03_02_gii_rear`                                                                                                                            | W/m²         |
| Solar                        | Deviation in POA irradiance across sensors vs. plant reference              | `meteorologicas_em_03_02_desviación_incidente`                                                                                                                | W/m²         |
| Solar                        | Ambient & Module Temperature from Weather/Soiling Stations                  | `meteorolgicas_em_03_02_t_amb`,<br>`celulas_ctin08_cc_08_2_t_amb`,<br>`celulas_ctin03_cc_03_1_t_mod`                                                          | °C           |
| Electrical                   | DC box current inputs from different SCBs                                   | `inversores_ctin03_strings_string8_pv_i*`,<br>`inversores_ctin08_strings_string9_pv_i*`                                                                      | A            |
| Electrical                   | DC box combined DC voltages                                                 | `inversores_ctin03_strings_string8_pv_v`,<br>`inversores_ctin08_strings_string9_pv_v`                                                                        | V            |
| Electrical                   | Inverter Total Active Energy Exported                                       | `inversores_ctin03_inv_03_03_eact_tot`,<br>`inversores_ctin08_inv_08_08_eact_tot`                                                                            | MW (15min)   |
| Electrical                   | Inverter DC input & AC Active power                                         | `inversores_ctin03_inv_03_03_p_dc`,<br>`inversores_ctin03_inv_03_03_p`                                                                                         | MW           |
| Soiling-vs Shading           | Clean, dirty reference cells                                                | `celulas_ctin03_cc_03_1_ir_cel_1`,<br>`celulas_ctin03_cc_03_1_ir_cel_2`                                                                                       | W/m²         |
| Geometry & Tracker           | Tracker Target, Actual angle & working mode                                | `seguidores_ct08_gcu081_t0808029_pos_obj`,<br>`seguidores_ct08_gcu081_t0808029_pos_ang`,<br>`seguidores_ct08_gcu081_t0808029_workingmode`                   | °            |
| Weather                      | Wind speed & direction                                                      | `meteorologicas_em_03_02_ws`,<br>`meteorologicas_em_03_02_wd`,<br>`celulas_ctin03_cc_03_2_wind_speed`                                                       | m/s, °       |
| Weather                      | Relative humidity                                                           | `meteorologicas_em_08_01_h_r`                                                                                                                                | %            |
| Weather                      | Datalogger temperature                                                      | `meteorologicas_em_08_01_t_dlogger`                                                                                                                           | °C           |
| Plant-level energy & compliance | Exported Energy from PPC to Plant                                        | `ppc_eact_export`                                                                                                                                             | MW (15min)   |
| Plant-level energy & compliance | Imported Energy to PPC from Plant (Actual Energy)                        | `ppc_eact_imp`                                                                                                                                                | MW (15min)   |
| Plant-level energy & compliance | PPC Active power setpoints                                               | `ppc_consig_p`                                                                                                                                                 | MW           |
| Plant-level energy & compliance | Total PPC Active Power                                                   | `ppc_p_tot`                                                                                                                                                    | MW           |
| Plant-level energy & compliance | Calculated Theoretical Power                                             | `ttr_potencia_producible`                                                                                                                                      | MW           |

---

## 🧠 Suggested Folder Structure

```text
solar-loss-attribution/
├── README.md
├── data/
│   └── raw/
├── notebooks/
│   └── loss_modeling.ipynb
├── src/
│   ├── preprocess.py
│   ├── loss_calculator.py
│   └── visualization.py
├── docs/
│   └── inverter_arrangement.png
├── requirements.txt

## 🧠 Assumptions (Add your own)

- Theoretical generation is modeled using POA Irradiance, Module Temperature, Tracker Angles, and Panel Specs  
- Losses are calculated per interval and aggregated over time  
- Boolean loss flags are determined using thresholds derived from data exploration

## 🛠️ Tools Recommended

- Python, Pandas, NumPy, Scikit-learn  
- Matplotlib / Plotly / Seaborn  
- PVLib (for theoretical solar modeling)  
- Jupyter Lab / VS Code (with GitHub Copilot or Codex)
