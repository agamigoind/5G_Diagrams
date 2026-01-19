```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant AMF as AMF (Serving Network)
    %% participant AUSF as AUSF (Home Network)
    %% participant SEAF as SEAF (in AMF)

    rect rgb(245,245,245)
    note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA' completed.<br/>Keys derived:<br/>K_AUSF → K_SEAF → K_AMF → (K_NAS-int, K_NAS-enc).<br/>UE and AMF hold NAS Security Context candidates identified by ngKSI.
    end

    UE-->>AMF: (Prior NAS signalling leading up to SMC)
    AMF->>AMF: Select NAS Security Context (ngKSI)<br/>Select NAS algorithms (NIA/NEA)<br/>Prepare UE Security Capabilities echo + ABBA

    rect rgb(230,250,255)
    AMF->>UE: NAS Security Mode Command (SMC)<br/>Integrity protected using K_NAS-int, typically not ciphered<br/>Includes ngKSI, selected NIA/NEA, UE security capabilities (replayed), ABBA, optional IMEISV req
    end

    UE->>UE: Verify NAS MAC (integrity check)<br/>Validate replayed UE security capabilities + ABBA (anti-bidding-down)<br/>Activate selected NIA/NEA locally

    alt UE accepts SMC
        rect rgb(230,255,230)
        UE->>AMF: NAS Security Mode Complete<br/>Integrity protected, ciphered if NEA is activated<br/>Includes UE response (e.g. IMEISV if requested), optional PDU session status
        end
        AMF->>AMF: Confirm UL activation, start DL NAS security with selected NIA/NEA
        AMF-->>UE: Subsequent NAS messages (e.g. Registration Accept)<br/>Integrity protected, ciphered if enabled
    else UE rejects SMC
        rect rgb(255,235,235)
        UE-->>AMF: NAS Security Mode Reject<br/>Integrity protected if possible, cause value
        end
        AMF->>AMF: Handle failure (e.g. reattempt, release)
    end
 
