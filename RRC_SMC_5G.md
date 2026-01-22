```mermaid
sequenceDiagram
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF

    Note over UE,AMF
      Precondition: 5G-AKA complete; AMF holds K_AMF and NAS security is active.
      Only NAS SMC can change the key set; RRC SMC cannot.
    end note

    AMF->>AMF: Derive K_gNB (or NH/NCC) from K_AMF

    alt Initial access (Registration / Initial Request)
        AMF->>gNB: NGAP Initial Context Setup\nK_gNB or NH/NCC + UE Security Capabilities
    else UE context update
        AMF->>gNB: NGAP UE Context Modification Request\nK_gNB or NH/NCC + UE Security Capabilities
    else Handover
        AMF->>gNB: NGAP Handover Request\nNH/NCC or target K_gNB + UE Security Capabilities
    end

    Note right of gNB: Store UE Security Capabilities and K_gNB/NH/NCC

    gNB->>UE: RRC Security Mode Command\nSelect NEA/NIA from UE capabilities (no key change)

    Note over UE
      Initialize UL/DL security with selected NEA/NIA
      using the current AS key hierarchy
    end note

    UE-->>gNB: RRC Security Mode Complete (ciphered + integrity protected)

    gNB->>gNB: Enable/confirm DL security with selected NEA/NIA

    Note over UE,gNB
      AS security active (UL & DL).
      RRC SMC selects algorithms only; NAS SMC changes key set when applicable.
    end note
```
