```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF (Serving Network)
    %% participant AUSF as AUSF (Home Network)
    %% participant SEAF as SEAF (in AMF)

    rect rgb(245,245,245)
    note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA prime completed. AMF holds K_AMF and NAS security is active. AMF derives AS key input for gNB as K_gNB or NH/NCC and prepares UE Security Capabilities for delivery to gNB.
    end

    UE-->>AMF: Prior NAS signalling leading up to initial access or context setup
    AMF->>AMF: Derive or access K_gNB (or NH/NCC) from K_AMF; prepare UE Security Capabilities

    alt Initial access (Registration / Initial Request)
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP Initial Context Setup including K_gNB or NH/NCC plus UE Security Capabilities plus NAS context references
        end
    else UE context update
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP UE Context Modification Request including K_gNB or NH/NCC plus UE Security Capabilities
        end
    else Handover
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP Handover Request including NH/NCC or target K_gNB plus UE Security Capabilities
        end
    end

    gNB->>gNB: Store UE Security Capabilities and key input (K_gNB or NH/NCC)

    rect rgb(245,255,245)
    gNB-->>UE: RRC Security Mode Command (SMC) selects NEA and NIA supported by UE (AS algorithms only)
    end

    UE->>UE: Initialize UL and DL AS security with selected NEA and NIA using the current AS key hierarchy
    rect rgb(230,255,230)
    UE-->>gNB: RRC Security Mode Complete (AS security confirmation)
    end

    gNB->>gNB: Enable and confirm DL AS security with selected NEA and NIA

    rect rgb(245,245,245)
    note over UE,gNB: Result: AS security active for UL and DL. RRC SMC selects and activates AS algorithms. NAS SMC governs NAS security activation and NAS key set changes.
    end

