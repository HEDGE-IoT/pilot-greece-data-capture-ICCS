# Data Capture

## 1.1 General Information and Purpose
- **Service Name/Title:** `data_capture`
- **Description and purpose:** Capture real-time data from edge devices.
- **Owner/Contact Information:** ICCS

---

## 1.2 Functional Requirements
1. Receive data from different edge devices in near real time.  
2. Validate data for anomalies and outliers.  
3. Harmonise the data.  
4. Store the captured data.  

---

## 1.3 Non-Functional Requirements
- Captured data should be available for processing within a few seconds.  
- Incoming data must be valid.  
- Access limited to data owners and system operators.  
- Periodic backups with automated recovery.  
- 99.9% uptime.  

---

## 1.4 Service Interfaces

### 1.4.1 API Endpoints

#### Endpoint 1 — Retrieve Device Consumption Data for Last Day
- **URL:** `/data/device/lastday/{deviceId}/{relay}`  
- **Method:** `GET`  
- **Description:** Retrieves device consumption data by hour for the past 24 hours.  
- **Headers:** `Authorization: Bearer <token>`  

**Path Parameters**  
- `deviceId`: Unique device identifier used in MQTT.  
- `relay`: Relay status (0 or 1).  

**Response Example**
```json
{
  "consumption": [0.39, 0.38, 0.5, 0.52],
  "labels": ["16:00", "17:00", "18:00", "19:00"]
}
```

**Error Handling**
```json
401 Unauthorized: { "error": "Unauthorized", "message": "Invalid token" }
```

---

#### Endpoint 2 — Retrieve Device Consumption Data for Period
- **URL:** `/data/device/period/{start_date}/{end_date}/{deviceId}/{relay}`  
- **Method:** `GET`  
- **Description:** Retrieves device consumption data by day for the requested period.  
- **Headers:** `Authorization: Bearer <token>`  

**Path Parameters**  
- `start_date`, `end_date`: Period range.  
- `deviceId`: Device identifier.  
- `relay`: Relay status.  

**Response Example**
```json
{
  "consumption": [5.74, 9.89, 7.03],
  "labels": ["05-11-2024", "06-11-2024", "07-11-2024"]
}
```

**Error Handling**
```json
401 Unauthorized: { "error": "Unauthorized", "message": "Invalid token" }
```

---

#### Endpoint 3 — List All Devices for a House
- **URL:** `/house/getalldevices/{houseId}`  
- **Method:** `GET`  
- **Description:** Lists all registered edge devices for a house.  
- **Headers:** `Authorization: Bearer <token>`  

**Path Parameters**  
- `houseId`: House identifier.  

**Response Example**
```json
[
  {
    "device_id": "hedge-iot-17",
    "device_types_id": "03bc7d39-f8c4-4e3a-aa3b-5f3d068036d6",
    "id": "8cab34a3-5179-4fe1-a59f-5dab1057d5c0",
    "ip": "",
    "is_em": true,
    "is_virtual": false,
    "name": "General Meter",
    "relay": 0,
    "rooms_id": "00000000-0000-0000-0000-000000000000"
  }
]
```

**Error Handling**
```json
401 Unauthorized: { "error": "Unauthorized", "message": "Invalid token" }
```

---

### 1.4.2 UI Mock-ups
*(Not applicable)*  

---

## 1.5 Data Model

### Entities and Relationships
- Users can manage one or more Houses.  
- Houses contain Devices (appliances, energy meters).  
- Users may join Energy Communities managed by Aggregators.  
- PV Parks and private PV Systems linked to communities or houses.  

### Database Schema
- **Real-Time Energy Measurements:** High-frequency data (current, voltage, power, energy).  
- **Aggregated Energy Usage:** Hourly/daily/quarterly summaries.  
- **Metadata Schema:** Tracks users, houses, devices, communities, and PV systems.  

---

## 1.6 Integration and Dependencies

### Roles
- **Aggregator / Resource Aggregator:** Collects and integrates data, assesses flexibility, formulates bids.  
- **Market Operator (HENEX):** Depends on forecast-informed bids for efficiency.  

### Domains
- **Forecasting Zone:** Operational region for forecasts.  
- **Metering Points:** Provide detailed energy consumption/production data.  
- **Disaggregated Output:** Provides appliance-level usage from NILM/disaggregation.  

---

## 1.7 Security and Privacy
- **TLS Encryption:** Enforces HTTPS across all endpoints.  
- **Keycloak Authentication:** Token-based identity management; tokens expire quickly for security.  
- **Access Control:** Restricted to authorized users.  

---
A dashboard was implemented for monitoring an EMQX cluster:  
- **Cluster Overview:** Active nodes, client connections, topic/subscription count.  
- **Metrics Dashboard:** Real-time performance (messages/sec, dropped, active connections).  
- **Clients Overview:** Details of connected MQTT clients (ID, IP, status, expiry).  

This validated the system’s capacity to manage IoT device traffic and ensure robust real-time data capture.  
