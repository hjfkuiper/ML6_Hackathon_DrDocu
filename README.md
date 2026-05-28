# ML6_Hackathon_DrDocu
Making Claude do the shittiest work

▗▖  ▗▖▗▖   ▄▄▄▄     ▗▖ ▗▖ ▗▄▖  ▗▄▄▖▗▖ ▗▖ ▗▄▖▗▄▄▄▖▗▖ ▗▖ ▗▄▖ ▗▖  ▗▖                                  
▐▛▚▞▜▌▐▌   █        ▐▌ ▐▌▐▌ ▐▌▐▌   ▐▌▗▞▘▐▌ ▐▌ █  ▐▌ ▐▌▐▌ ▐▌▐▛▚▖▐▌                                  
▐▌  ▐▌▐▌   █▀▀█     ▐▛▀▜▌▐▛▀▜▌▐▌   ▐▛▚▖ ▐▛▀▜▌ █  ▐▛▀▜▌▐▌ ▐▌▐▌ ▝▜▌                                  
▐▌  ▐▌▐▙▄▄▖█▄▄█     ▐▌ ▐▌▐▌ ▐▌▝▚▄▄▖▐▌ ▐▌▐▌ ▐▌ █  ▐▌ ▐▌▝▚▄▞▘▐▌  ▐▌                                  
                                                                                                   
                                                                                                   
                                                                                                   
▗▄▄▄  ▗▄▄▖     ▗▄▄▄   ▗▄▖  ▗▄▄▖ ▗▄▄▖     ▗▄▄▖ ▗▄▖ ▗▄▄▄▖ ▗▄▄▖    ▗▄▄▖ ▗▄▄▖ ▗▄▄▖ ▗▄▄▖ ▗▄▄▖ ▗▄▄▖ ▗▄▄▖ 
▐▌  █ ▐▌ ▐▌    ▐▌  █ ▐▌ ▐▌▐▌   ▐▌       ▐▌   ▐▌ ▐▌▐▌   ▐▌       ▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌
▐▌  █ ▐▛▀▚▖    ▐▌  █ ▐▌ ▐▌▐▌    ▝▀▚▖    ▐▌▝▜▌▐▌ ▐▌▐▛▀▀▘ ▝▀▚▖    ▐▛▀▚▖▐▛▀▚▖▐▛▀▚▖▐▛▀▚▖▐▛▀▚▖▐▛▀▚▖▐▛▀▚▖
▐▙▄▄▀ ▐▌ ▐▌    ▐▙▄▄▀ ▝▚▄▞▘▝▚▄▄▖▗▄▄▞▘    ▝▚▄▞▘▝▚▄▞▘▐▙▄▄▖▗▄▄▞▘    ▐▙▄▞▘▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌▐▌ ▐▌
                                                                                                   
                                                                                                   
                                                                                                   

TLD, LD en TO — documenthiërarchie voor complexe IT-programma's
TLD — Top Level Design
Het TLD beschrijft het volledige systeem op programmaniveau. Het geeft antwoord op de vragen wat bouwen we, voor wie en waarom. Typische inhoud: architectuurprincipes, scope en grenzen van het systeem, de gekozen technologiestacks op hoofdlijn, stakeholders, risico's en een register van alle onderliggende documenten.
Een TLD is stabiel. Hij verandert alleen als de scope of architectuur fundamenteel wijzigt. Per programma is er één TLD.

LD — Level Design
Een LD werkt een specifiek domein, fase of deelgebied uit. Het beantwoordt wanneer en in welke context bepaalde onderdelen een rol spelen. Het verbindt de architectuurprincipes uit het TLD met de technische details in de TOs.
Voorbeelden van LD-indelingen: per lifecycle-fase (installatie, operatie, uitfasering), per platformlaag (infrastructuur, middleware, applicatie), of per functioneel domein (netwerk, identiteit, security).
Per programma zijn er meerdere LDs — afhankelijk van hoe het systeem is opgedeeld.

TO — Technisch Ontwerp
Een TO beschrijft één specifieke component of dienst volledig. Het beantwoordt hoe die component werkt: requirements, interfaces met andere componenten, architectuurbesluiten met motivatie, compliance-toetsing en eventuele varianten.
Een TO is het werkniveau van engineers. Per component of dienst is er één TO. Bij 20 componenten zijn er dus 20 TOs.

De relatie in één schema
TLD  →  wat bouwen we, waarom, voor wie        (1 document)
 └─ LD  →  welk domein / fase / laag           (enkele documenten)
     └─ TO  →  hoe werkt dit specifieke onderdeel  (veel documenten)
De hiërarchie werkt ook als leeswijzer: managers lezen de TLD, architecten lezen LDs, engineers leven in de TOs.
