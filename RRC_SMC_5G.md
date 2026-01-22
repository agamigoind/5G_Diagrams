```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF (Serving Network)
    %% participant AUSF as AUSF (Home Network)
    %% participant SEAF as SEAF (in AMF)

    rect rgb(245,245,245)
    note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA' completed.<br/>AMF holds K_AMF; NAS security active.<br/>AMF derives AS key input for target gNB: K_gNB or NH/NCC (as applicable).<br/>UE Security Capabilities are available for delivery to gNB.
    end

    UE-->>AMF: (Prior NAS signalling leading up to initial access / context setup)
    AMF->>AMF: Derive/access K_gNB (or NH/NCC) from K_AMF<br/>Prepare UE Security Capabilities for delivery to gNB

    alt Initial access (Registration / Initial Request)
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP Initial Context Setup<br/>Includes K_gNB or NH/NCC + UE Security Capabilities + NAS context references
        end
    else UE context update
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP UE Context Modification Request<br/>Includes K_gNB or NH/NCC + UE Security Capabilities
        end
    else Handover
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP Handover Request<br/>Includes NH/NCC or target K_gNB + UE Security Capabilities
        end
    end

    gNB->>gNB: Store UE Security Capabilities and key input (K_gNB / NH / NCC)

    rect rgb(245,255,245)
    gNB-->>UE: RRC Security Mode Command (SMC)<br/>Select NEA/NIA supported by UE; configure RRC/UP security algorithms (no NAS key change here)
    end

    UE->>UE: Initialize UL/DL AS security with selected NEA/NIA<br/>Using current AS key hierarchy (based on K_gNB or NH/NCC)

    rect rgb(230,255,230)
    UE-->>gNB: RRC Security Mode Complete<br/>Integrity protected and ciphered as per selected NIA/NEA (AS security confirmation)
    end

    gNB->>gNB: Enable/confirm DL AS security with selected NEA/NIA

    rect rgb(245,245,245)
    note over UE,gNB: Result: AS security active for UL and DL.<br/>RRC SMC selects algorithms and activates AS protection at RRC/PDCP.<br/>NAS SMC is the procedure that governs NAS security activation and NAS key set changes.
    end

