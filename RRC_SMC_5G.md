```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF (Serving Network)

    rect rgb(245,245,245)
   note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA' completed.<br/>NAS security active; AMF holds K_AMF.<br/>AS key material available: K_gNB or NH/NCC derived from K_AMF.<br/>UE Security.
 end

    UE-->>AMF: (Prior NAS signalling leading up to initial access / context setup)
    AMF->>AMF: Derive K_gNB or NH/NCC from K_AMF<br/>Select target AS key set<br/>Prepare UE Security Capabilities for NGAP signalling

    alt Initial access (Registration / Initial Request)
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP Initial Context Setup<br/>Includes K_gNB or NH/NCC<br/>Includes UE Security Capabilities<br/>Includes NAS context references
        end
    else UE context update
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP UE Context Modification Request<br/>Includes K_gNB or NH/NCC<br/>Includes UE Security Capabilities
        end
    else Handover
        rect rgb(230,250,255)
        AMF-->>gNB: NGAP Handover Request<br/>Includes NH/NCC or target K_gNB<br/>Includes UE Security Capabilities
        end
    end

    gNB->>gNB: Store UE Security Capabilities<br/>Store AS key input (K_gNB or NH/NCC)

    rect rgb(245,255,245)
    gNB-->>UE: RRC Security Mode Command (SMC)<br/>Select AS algorithms (NEA/NIA) supported by UE<br/>No NAS key set change
    end

    UE->>UE: Verify RRC SMC<br/>Activate selected NEA/NIA locally<br/>Initialize UL and DL AS security

    rect rgb(230,255,230)
    UE-->>gNB: RRC Security Mode Complete<br/>Integrity protected and ciphered according to selected NIA/NEA
    end

    gNB->>gNB: Confirm UL activation<br/>Enable DL AS security with selected NEA/NIA

    rect rgb(245,245,245)
    note over UE,gNB: Result: AS security active for UL and DL.<br/>RRC SMC selects and activates AS algorithms.<br/>NAS SMC governs NAS security activation and NAS key set changes.
    end
end
