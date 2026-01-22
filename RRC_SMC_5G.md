```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF (Serving Network)
    %% participant AUSF as AUSF (Home Network)
    %% participant SEAF as SEAF (in AMF)

    rect rgb(245,245,245)
    note over UE,AMF: Pre-condition: 5G-AKA or EAP-AKA' completed.&lt;br/&gt;AMF holds K\_AMF; NAS security is active.&lt;br/&gt;AS key seed for gNB will be K\_gNB or NH/NCC as applicable.
    end

    UE--&gt;&gt;AMF: (Prior NAS signalling leading up to initial access / context setup)
    AMF-&gt;&gt;AMF: Derive/access K\_gNB (or NH/NCC) from K\_AMF&lt;br/&gt;Prepare UE Security Capabilities for delivery to gNB

    rect rgb(230,250,255)
    alt Initial access (Registration / Initial Request)
        AMF--&gt;&gt;gNB: NGAP Initial Context Setup&lt;br/&gt;Includes K\_gNB or NH/NCC + UE Security Capabilities + NAS context refs
    else UE context update
        AMF--&gt;&gt;gNB: NGAP UE Context Modification Request&lt;br/&gt;Includes K\_gNB or NH/NCC + UE Security Capabilities
    else Handover
        AMF--&gt;&gt;gNB: NGAP Handover Request&lt;br/&gt;Includes NH/NCC or target K\_gNB + UE Security Capabilities
    end
    end

    gNB-&gt;&gt;gNB: Store UE Security Capabilities and key input (K\_gNB/NH/NCC)

    rect rgb(245,255,245)
    gNB--&gt;&gt;UE: RRC Security Mode Command (SMC)&lt;br/&gt;Select NEA/NIA supported by UE; no key set change at RRC (algorithms only)
    end

    UE-&gt;&gt;UE: Initialize UL/DL security with selected NEA/NIA&lt;br/&gt;Using current AS key hierarchy (no key set change)

    rect rgb(230,255,230)
    UE--&gt;&gt;gNB: RRC Security Mode Complete&lt;br/&gt;Ciphered and integrity protected (confirmation)
    end

    gNB-&gt;&gt;gNB: Enable/confirm DL security with selected NEA/NIA

    note over UE,gNB: Result: AS security active for UL &amp; DL.&lt;br/&gt;RRC SMC selects algorithms only; NAS SMC governs key set changes.
```
