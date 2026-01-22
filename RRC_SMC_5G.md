```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF (Serving Network)
    %% participant AUSF as AUSF (Home Network)
    %% participant SEAF as SEAF (in AMF)

    rect rgb(245,245,245)
    note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA' completed.<br/>AMF holds K_AMF; NAS security is active.<br/>AS key seed for gNB will be K_gNB or NH/NCC as applicable.
    end

    UE-->>AMF: (Prior NAS signalling leading up to initial access / context setup)
    AMF->>AMF: Derive/access K_gNB (or NH/NCC) from K_AMF<br/>Prepare UE Security Capabilities for delivery to gNB

    rect rgb(230,250,255)
    alt Initial access (Registration / Initial Request)
        AMF-->>gNB: NGAP Initial Context Setup<br/>Includes K_gNB or NH/NCC + UE Security Capabilities + NAS context refs
    else UE context update
        AMF-->>gNB: NGAP UE Context Modification Request<br/>Includes K_gNB or NH/NCC + UE Security Capabilities
    else Handover
        AMF-->>gNB: NGAP Handover Request<br/>Includes NH/NCC or target K_gNB + UE Security Capabilities
    end
    end

    gNB->>gNB: Store UE Security Capabilities and key input (K_gNB/NH/NCC)

    rect rgb(245,255,245)
    gNB-->>UE: RRC Security Mode Command (SMC)<br/>Select NEA/NIA supported by UE; no key set change at RRC (algorithms only)
    end

    UE->>UE: Initialize UL/DL security with selected NEA/NIA<br/>Using current AS key hierarchy (no key set change)

    rect rgb(230,255,230)
    UE-->>gNB: RRC Security Mode Complete<br/>Ciphered and integrity protected (confirmation)
    end

    gNB->>gNB: Enable/confirm DL security with selected NEA/NIA

    note over UE,gNB: Result: AS security active for UL & DL.<br/>RRC SMC selects algorithms only; NAS SMC governs key set changes.
