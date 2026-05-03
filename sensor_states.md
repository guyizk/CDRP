# ELINT Sensor State Diagram

```mermaid
stateDiagram-v2
    direction TB

    state "INIT MODE" as Category_Init {
        [*] --> Power_Up
        Power_Up --> Init: Automatic
        Init --> OP_STDBY: Success ACK
    }

    state "OPERATIONAL MODE" as Category_Op {
        OP_STDBY --> Scanning: Scan Request
        Scanning --> Scanning: New Scan Request
        Scanning --> OP_STDBY: Stop Request
    }

    state "MAINTENANCE MODE" as Category_Maint {
        MAINT_STDBY --> IBIT: Run IBIT
        MAINT_STDBY --> Calibration: Start CAL
        
        IBIT --> MAINT_STDBY: Complete (Pass)
        Calibration --> MAINT_STDBY: Complete (Pass)

        %%state "IDLE / Waiting" as MAINT_STDBY
    }

    %%state "ERROR & FAULT" as Category_Error {
    %%    Error_Fault: ERROR / FAULT
    %%}

    %% Main Transitions between categories
    OP_STDBY --> MAINT_STDBY: Maint Request
    MAINT_STDBY --> OP_STDBY: OP Request

    %% Fault Transitions
    %%Init --> Error_Fault: Fault Detected
    %%Scanning --> Error_Fault: Fault Detected
    %%Calibration --> Error_Fault: Fault Detected (Fail)

    %% Note: No transition from MAINT_STDBY to Error_Fault as it is IDLE