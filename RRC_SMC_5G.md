```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF (Serving Network)
    %% participant AUSF as AUSF (Home Network)
    %% participant SEAF as SEAF (in AMF)

    rect rgb(245,245,245)
        note over UE,AMF
            Pre-condition: 5G-AKA or EAP-AKA' completed.
            AMF holds K_AMF; NAS security is active.
            AS key seed for gNB will be K_gNB or NH/NCC as applicable.
        end
    end

    UE-->>AMF: Prior NAS signalling leading up to initial access / context setup
    AMF->>AMF: Derive or access K_gNB (or NH/NCC) from K_AMF; prepare UE Security Capabilities

    rect rgb(230,250,255)
        alt Initial access (Registration / Initial Request)
            AMF-->>gNB: NGAP Initial Context Setup (K_gNB or NH/NCC + UE Security Capabilities + NAS refs)
        else UE context update
            AMF-->>gNB: NGAP UE Context Modification Request (K_gNB or NH/NCC + UE Security Capabilities)
        else Handover
            AMF-->>gNB: NGAP Handover Request (NH/NCC or target K_gNB + UE Security Capabilities)
        end
    end

    gNB->>gNB: Store UE Security Capabilities and key input (K_gNB / NH / NCC)

    rect rgb(245,255,245)
        gNB-->>UE: RRC Security Mode Command (select NEA/NIA; algorithms only)
    end

    UE->>UE: Initialize UL/DL security with selected NEA/NIA

    rect rgb(230,255,230)
        UE-->>gNB: RRC Security Mode Complete (ciphered and integrity protected)
    end

    gNB->>gNB: Enable and confirm DL security with selected NEA/NIA

    note over UE,gNB
        Result:
        AS security active for UL and DL.
        RRC SMC selects algorithms only.
        NAS SMC governs key set changes.
    end
