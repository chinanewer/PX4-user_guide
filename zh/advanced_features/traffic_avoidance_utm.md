# Air Traffic Avoidance: UAS Traffic Management (UTM)

PX4 can use MAVLink [`UTM_GLOBAL_POSITION`](https://mavlink.io/en/messages/common.html#UTM_GLOBAL_POSITION) messages to support simple air traffic avoidance in [missions](../flight_modes/mission.md). If a potential collision is detected, PX4 can *warn*, immediately [land](../flight_modes/land.md), or [return](../flight_modes/return.md) (depending on the value of [`NAV_TRAFF_AVOID`](#NAV_TRAFF_AVOID)).

:::note
This implementation is exactly the same as for [ADS-B traffic avoidance](../advanced_features/traffic_avoidance_adsb.md) (except for the source of other-vehicle data). For more information see [implementation](#implementation) below.
:::


## Configure Traffic Avoidance

Configure the trigger distance and action when there is a potential collision using the parameters below:

| Parameter                                                                                                 | Description                                                                                                                                                                       |
| --------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| <a id="NAV_TRAFF_AVOID"></a>[NAV_TRAFF_AVOID](../advanced_config/parameter_reference.md#NAV_TRAFF_AVOID)   | Enable traffic avoidance mode specify avoidance response. 0: Disable, 1: Warn only, 2: Return mode, 3: Land mode.                                                                 |
| <a id="NAV_TRAFF_A_HOR"></a>[NAV_TRAFF_A_HOR](../advanced_config/parameter_reference.md#NAV_TRAFF_A_HOR)   | Horizonal radius of cylinder around the vehicle that defines its airspace (i.e. the airspace in the ground plane).                                                                |
| <a id="NAV_TRAFF_A_VER"></a>[NAV_TRAFF_A_VER](../advanced_config/parameter_reference.md#NAV_TRAFF_A_VER)   | Vertical height above and below vehicle of the cylinder that defines its airspace (also see [NAV_TRAFF_A_HOR](#NAV_TRAFF_A_HOR)).                                               |
| <a id="NAV_TRAFF_COLL_T"></a>[NAV_TRAFF_COLL_T](../advanced_config/parameter_reference.md#NAV_TRAFF_COLL_T) | Collision time threshold. Avoidance will trigger if the estimated time until collision drops below this value (the estimated time is based on relative speed of traffic and UAV). |


## Implementation

PX4 listens for `UTM_GLOBAL_POSITION` MAVLink messages during missions. When a valid message is received, its validity flags, position and heading are mapped into the same `transponder_report` UORB topic used for *ADS-B traffic avoidance*.

The implementation is otherwise *exactly* as described in: [ADS-B traffic avoidance > Implementation](../advanced_features/traffic_avoidance_adsb.md#implementation).

:::note
[UTM_GLOBAL_POSITION](https://mavlink.io/en/messages/common.html#UTM_GLOBAL_POSITION) contains additional fields that are not provided by an ADSB transponder (see [ADSB_VEHICLE](https://mavlink.io/en/messages/common.html#ADSB_VEHICLE)). The current implementation simply drops the additional fields (including information about the vehicle's planned next waypoint).
:::

## Further Information

- [ADS-B Traffic Avoidance](../advanced_features/traffic_avoidance_adsb.md)
