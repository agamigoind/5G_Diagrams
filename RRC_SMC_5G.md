
```mermaid
sequenceDiagram
    autonumber
    participant UE as UE
    participant gNB as gNB
    participant AMF as AMF
    %% ------------------------------------------------------------------
    %% Preconditions and keying context
    %% ------------------------------------------------------------------
    rect rgb(245,245,245)
    note over UE,AMF: Pre-condition: 5G-AKA/EAP-AKA' completed; NAS security active.&lt;br/&gt;AMF holds K_AMF; AS key seed for gNB will be K_gNB or NH/NCC as applicable.
    end

    %% AMF derives/accesses keying material and delivers via NGAP
    AMF-&gt;&gt;AMF: Derive/access K_gNB (or NH/NCC) from K_AMF

    alt Initial access (Registration / Initial Request)
        rect rgb(230,250,255)
        AMF--&gt;&gt;gNB: NGAP Initial Context Setup&lt;br/&gt;Includes K_gNB or NH/NCC + UE Security Capabilities + NAS context refs
        end
    else UE context update
        rect rgb(230,250,255)
        AMF--&gt;&gt;gNB: NGAP UE Context Modification Request&lt;br/&gt;Includes K_gNB or NH/NCC + UE Security Capabilities
        end
    else Handover
        rect rgb(230,250,255)
        AMF--&gt;&gt;gNB: NGAP Handover Request&lt;br/&gt;Includes NH/NCC or target K_gNB + UE Security Capabilities
        end
    end

    note right of gNB: gNB stores UE Security Capabilities and key input (K_gNB/NH/NCC)

    %% ------------------------------------------------------------------
    %% RRC SMC: algorithm selection only (no key set change)
    %% ------------------------------------------------------------------
    rect rgb(245,255,245)
    gNB--&gt;&gt;UE: RRC Security Mode Command (SMC)&lt;br/&gt;Select NEA/NIA supported by UE; no key set change (RRC cannot change keys)
    end

    UE-&gt;&gt;UE: Initialize UL/DL security with selected NEA/NIA&lt;br/&gt;Using current AS key hierarchy

    rect rgb(230,255,230)
    UE--&gt;&gt;gNB: RRC Security Mode Complete&lt;br/&gt;Ciphered and integrity protected (confirmation)
    end

    gNB-&gt;&gt;gNB: Enable/confirm DL security with selected NEA/NIA

    note over UE,gNB: Result: AS security active for UL &amp; DL.&lt;br/&gt;RRC SMC selects algorithms only; NAS SMC governs key set changes.
```
``

