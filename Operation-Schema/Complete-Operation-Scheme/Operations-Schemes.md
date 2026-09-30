
# Complete Operation Schema – Smart Museum Artifact Conservation System

Format: Precondition → Operation → Post-condition

| Id | Precondition | Events/Input | Post-conditions |
|----|--------------|--------------|-----------------|
| OP01 performSelfCheck | Chamber just powered on | Power-on event; sensor and device status | If all essential sensors pass: sensorsOK = true, mode = MONITORING. Otherwise: sensorsOK = false, fault reported, conservation not allowed |
| OP02 registerArtifact | mode = MONITORING; no artifact registered | Artifact placed; artifact ID; tempMin/Max, humMin/Max, vibLimit | Artifact record stored, profileLoaded = true, monitoring of artifact begins |
| OP03 activateConservation | mode = MONITORING; doorClosed; profileLoaded; sensorsOK | Door closed event | mode = CONSERVATION_ACTIVE. If door is open, mode stays MONITORING |
| OP04 evaluateEnvironment | mode = CONSERVATION_ACTIVE | Temperature and humidity readings | tempOutOfRange / humOutOfRange flags updated; recovery timer starts for new out-of-range condition |
| OP05 correctTemperature | mode = CONSERVATION_ACTIVE; tempOutOfRange = true | Temperature reading; tempMin/Max | Corrective heating/cooling command issued; condition NOT yet considered resolved |
| OP06 correctHumidity | mode = CONSERVATION_ACTIVE; humOutOfRange = true | Humidity reading; humMin/Max | Corrective humidity command issued; condition NOT yet considered resolved |
| OP07 verifyRecovery | Correction command issued; recovery timer running | Fresh sensor readings; recoveryLimit | In range: flag cleared, timer stopped. Limit exceeded: recoveryFailed = true |
| OP08 initiateProtection | recoveryFailed = true; mode = CONSERVATION_ACTIVE | Recovery-failed event | mode = PROTECTION_MODE, protectionActive = true, lightLevel reduced, additional controls activated |
| OP09 raiseOperatorAlert | protectionActive = true | Cause, artifact ID, current readings | Alert sent to operator and logged |
| OP10 respondToVibration | Artifact present in chamber | Vibration reading > vibLimit | mode = VIBRATION_RESPONSE, risky activities suspended, stabilization timer reset |
| OP11 verifyVibrationStability | mode = VIBRATION_RESPONSE | Vibration readings; stabilizationPeriod | Below limit for full period: activities released, proceed to resume checks. Vibration rises again: timer resets, mode stays VIBRATION_RESPONSE |
| OP12 suspendOnDoorOpen | mode = CONSERVATION_ACTIVE | Door opened event | doorClosed = false, normal environmental operation suspended, mode = DOOR_OPEN_SUSPENDED |
| OP13 verifyResumeConditions | mode = DOOR_OPEN_SUSPENDED (or post-vibration); doorClosed = true | Door closed event; sensor status; temperature and humidity readings | Sensors OK and conditions in range: mode = CONSERVATION_ACTIVE. Otherwise: correction or protection triggered |
| OP14 switchToEmergencyPower | Main power lost during conservation; emergencyPowerAvailable = true | Power-loss event | Emergency source active, conservation continues, event logged |
| OP15 performSafeShutdown | Main power lost; emergencyPowerAvailable = false | Power-loss event | Incident recorded, devices set to safe state, mode = SAFE_SHUTDOWN |
| OP16 releaseArtifact | Operator requests removal; protectionActive = false; mode not PROTECTION_MODE or VIBRATION_RESPONSE; chamber safe | Operator removal request | Removal permitted and artifact record cleared. Otherwise request denied with reason |
