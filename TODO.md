# Remeha Home API — Feature Tracker

## Implemented ✅

### Climate Zone Controls
| API Endpoint | HA Entity | Status |
|---|---|---|
| `/climate-zones/{id}/modes/manual` | HVAC mode: HEAT | ✅ `ClimateEntity.hvac_modes` |
| `/climate-zones/{id}/modes/schedule` | HVAC mode: AUTO | ✅ Preset buttons trigger this |
| `/climate-zones/{id}/modes/temporary-override` | Set temperature in AUTO mode | ✅ `async_set_temperature` |
| `/climate-zones/{id}/modes/anti-frost` | HVAC mode: OFF | ✅ `ClimateEntity` turn off |
| `/climate-zones/{id}/time-programs/heating/{id}/activate` | Presets (clock_program_1/2/3) | ✅ `preset_mode` |
| `/climate-zones/{id}/modes/fireplacemode` | Switch entity | ✅ `RemehaHomeFireplaceModeSwitch` |
| `/climate-zones/{id}/heating-curve` GET/SET | Service + sensors | ✅ `set_heating_curve` service + slope/base/maxFlowTemp sensors |

### Sensors (Dashboard data)
| Field | Entity | Status |
|---|---|---|
| `climateZone.roomTemperature` | Sensor | ✅ Auto-exposed via climate entity |
| `climateZone.nextSetpoint` | Sensor | ✅ `Next Setpoint` |
| `climateZone.nextSwitchTime` | Sensor | ✅ `Next Setpoint Time` |
| `climateZone.currentScheduleSetPoint` | Sensor | ✅ `Current Schedule Setpoint` |
| `hotWaterZone.dhwTemperature` | Sensor | ✅ `DHW Water Temperature` (may show unknown) |
| `appliance.waterPressure` | Sensor | ✅ `Water Pressure` |
| `appliance.outdoorTemperatureInformation.applianceOutdoorTemperature` | Sensor | ✅ `Home Outdoor Temperature` |
| `appliance.outdoorTemperatureInformation.cloudOutdoorTemperature` | Sensor | ✅ `Cloud Outdoor Temperature` (hidden by default) |

### Energy Consumption
| API Endpoint | Entity | Status |
|---|---|---|
| `/energyconsumption/daily?today` | Sensor | ✅ Every 15 min (hidden by default) |
| `/energyconsumption/daily` yesterday | Sensor | ✅ Yesterday — aggregated (hidden by default) |
| `/energyconsumption/monthly` | Sensor | ✅ Month-to-date — aggregated (hidden by default) |
| `/energyconsumption/yearly` | Sensor | ✅ Year-to-date — aggregated (hidden by default) |

### Other
| Feature | Status |
|---|---|
| OAuth2 B2C login flow | ✅ Implemented in `RemehaHomeOAuth2Implementation` |
| Holiday schedule read from dashboard | ✅ Parsed but no entity exposed |

---

## NOT Implemented ❌

### Hot Water Zone Controls
| API Endpoint | What it does | Notes |
|---|---|---|
| `/hot-water-zones/{id}/modes/anti-frost` | Eco mode on DHW | Simple call, one of 3 states |
| `/hot-water-zones/{id}/modes/schedule` | Schedule mode on DHW | Follows daily program |
| `/hot-water-zones/{id}/modes/continuous-comfort` | Comfort mode on DHW | Always heat to comfort setpoint |
| `/hot-water-zones/{id}/reduced-setpoint` | Set eco target temp (40–60 range) | Mirrors existing setpoint pattern |
| `/hot-water-zones/{id}/comfort-setpoint` | Set comfort target (40–65 range) | Mirror existing setpoint pattern |

**Quick win:** Add a Select entity for hot water mode (Eco/Schedule/Comfort), plus optional number entities for reduced/comfort setpoints.

### Energy Consumption Gaps
| API Endpoint | What it adds | Effort |
|---|---|---|
| ~~`/energyconsumption/daily?yesterday`~~ | ~~"Yesterday" sensor~~ | ✅ Done (v0.1.20) |
| ~~`/energyconsumption/monthly?...`~~ | ~~Cumulative month-to-date~~ | ✅ Done (v0.1.20) |
| ~~`/energyconsumption/yearly`~~ | ~~Year-to-date~~ | ✅ Done (v0.1.20) |

### Holiday Schedule
| API Endpoint | What it does | Effort |
|---|---|---|
| `PUT /homes/{id}/holiday` (likely) | Activate/deactivate holiday/away mode | Needs discovery — probably exists as endpoint |
| Already received in dashboard | `holidaySchedule.active`, `startTime`, `endTime` | Read-only entity = zero new calls |

### Auto-Fill Mode
| Dashboard field | What it shows | Notes |
|---|---|---|
| `autoFilling.mode` / `status` | Auto-refill water pressure status | If supported, expose as sensor/toggle |

### Capability-Agnostic Discovery
Dashboard exposes per-device feature flags — currently ignored:
| Flag | Description | Impact if unused |
|---|---|---|
| `capabilityCooling` | System supports cooling | Might enable cooling HVAC modes |
| `capabilityPreHeat` | Pre-heating logic available | Could expose preheat toggle |
| `capabilityPowerSettings` | Power level control | Unknown without docs |
| `capabilityInternetOutdoorTemperatureExpected` | External weather feed supported | Used for outdoor temp source selection |
| `utilizeOutdoorTemperature` | Whether outdoor temp is factored into heating curve | Relevant for heating curve tuning |

### Solar Thermal
| Field | Value | Notes |
|---|---|---|
| `/homes/dashboard → solarThermals` | Empty array `[]` | Only relevant if you have solar panels |

---

## Quick Wins (easiest to implement)
1. **"Yesterday" energy sensor** — ✅ done (v0.1.20) — also month/year totals added
2. **Holiday schedule read-only sensor** — data already arrives in every dashboard poll
3. **Hot water mode select** — 2 API calls needed, one sensor read, mirrors existing patterns
4. **HAWK sensor** — auto-expose `autoFilling.status` as a text/number sensor
