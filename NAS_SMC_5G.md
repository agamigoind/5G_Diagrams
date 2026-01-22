```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant AMF as AMF (Serving Network)
    %% participant AUSF as AUSF (Home Network)
    %% participant SEAF as SEAF (in AMF)

    rect rgb(245,245,245)
    note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA' completed.&lt;br/&gt;Keys derived:&lt;br/&gt;K_AUSF → K_SEAF → K_AMF → (K_NAS-int, K_NAS-enc).&lt;br/&gt;UE and AMF hold NAS Security Context candidates identified by ngKSI.
    end

    UE--&gt;&gt;AMF: (Prior NAS signalling leading up to SMC)
    AMF-&gt;&gt;AMF: Select NAS Security Context (ngKSI)&lt;br/&gt;Select NAS algorithms (NIA/NEA)&lt;br/&gt;Prepare UE Security Capabilities echo + ABBA

    rect rgb(230,250,255)
    AMF-&gt;&gt;UE: NAS Security Mode Command (SMC)&lt;br/&gt;Integrity protected using K_NAS-int, typically not ciphered&lt;br/&gt;Includes ngKSI, selected NIA/NEA, UE security capabilities (replayed), ABBA, optional IMEISV req
    end

    UE-&gt;&gt;UE: Verify NAS MAC (integrity check)&lt;br/&gt;Validate replayed UE security capabilities + ABBA (anti-bidding-down)&lt;br/&gt;Activate selected NIA/NEA locally

    alt UE accepts SMC
        rect rgb(230,255,230)
        UE-&gt;&gt;AMF: NAS Security Mode Complete&lt;br/&gt;Integrity protected, ciphered if NEA is activated&lt;br/&gt;Includes UE response (e.g. IMEISV if requested), optional PDU session status
        end
        AMF-&gt;&gt;AMF: Confirm UL activation, start DL NAS security with selected NIA/NEA
        AMF--&gt;&gt;UE: Subsequent NAS messages (e.g. Registration Accept)&lt;br/&gt;Integrity protected, ciphered if enabled
    else UE rejects SMC
        rect rgb(255,235,235)
        UE--&gt;&gt;AMF: NAS Security Mode Reject&lt;br/&gt;Integrity protected if possible, cause value
        end
        AMF-&gt;&gt;AMF: Handle failure (e.g. reattempt, release)
    end
