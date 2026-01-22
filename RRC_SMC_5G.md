```mermaid
sequenceDiagram
    title 5G AS Security Setup after 5G-AKA (UE, gNB, AMF)
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF

    note over UE,AMF: Precondition: 5G-AKA complete; AMF holds K_AMF and NAS security is active.\nOnly NAS SMC can change the key set; RRC SMC cannot.

    AMF->>AMF: Derive K_gNB (or NH/NCC) from K_AMF

    alt Initial access (Registration / Initial Request)
        AMF->>gNB: NGAP Initial Context Setup\n(K_gNB or NH/NCC,\nUE Security Capabilities, NAS context)
    else UE context update
        AMF->>gNB: NGAP UE Context Modification Request\n(K_gNB or NH/NCC,\nUE Security Capabilities)
    else Handover
        AMF->>gNB: NGAP Handover Request\n(NH/NCC or target K_gNB,\nUE Security Capabilities)
    end

    note right of gNB: gNB stores UE Security Capabilities\nand keying input (K_gNB or NH/NCC)

    gNB->>UE: RRC Security Mode Command\n(select NEA/NIA from UE capabilities;\nno key change, reuse current key set)

    note over UE: Initialize UL/DL security with selected NEA/NIA\nusing current AS key hierarchy

    UE-->>gNB: RRC Security Mode Complete\n(ciphered + integrity protected)

    gNB->>gNB: Enable/confirm DL security with selected NEA/NIA

    note over UE,gNB: AS security active (UL & DL).\nRRC SMC selects algorithms only; NAS SMC changes key set when applicable.
