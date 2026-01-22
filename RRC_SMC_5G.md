```mermaid
sequenceDiagram
    autonumber
    participant UE
    participant gNB
    participant AMF

    note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA prime completed; AMF holds K_AMF; NAS security active; AS key seed is K_gNB or NH/NCC

    UE-->>AMF: Prior NAS signalling before initial access
    AMF->>AMF: Derive K_gNB or NH/NCC from K_AMF and prepare UE Security Capabilities

    rect rgb(230,250,255)
        alt Initial access
            AMF-->>gNB: NGAP Initial Context Setup (K_gNB or NH/NCC, UE Security Capabilities)
        else UE context update
            AMF-->>gNB: NGAP UE Context Modification Request
        else Handover
            AMF-->>gNB: NGAP Handover Request (NH/NCC or target K_gNB)
        end
    end

    gNB->>gNB: Store UE Security Capabilities and AS key input

    gNB-->>UE: RRC Security Mode Command (select NEA/NIA only)
    UE->>UE: Initialize UL and DL AS security
    UE-->>gNB: RRC Security Mode Complete
    gNB->>gNB: Enable DL security

    note over UE,gNB: Result: AS security active for UL and DL; RRC SMC selects algorithms only; NAS SMC handles key changes
