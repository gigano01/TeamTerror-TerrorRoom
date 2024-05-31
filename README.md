# TeamTerror-TerrorRoom
De TerrorRoom is een interactieve simulatie over prikkelverwerking bij ASS, ADHD en HSP. Met deze installatie willen we aandacht vestigen op de dagelijkse problemen en moeilijkheden die deze mensen ervaren. We brengen niet alleen aandacht aan hun problemen, maar ook voor de vooroordelen waarmee ze dagelijks te maken hebben.

Spelers worden in een donkere kamer geplaatst waarin een klas wordt gesimuleerd. Terwijl een luidruchtige video met achtergrondgeluiden speelt, moeten ze een geheugenspel spelen. Tegelijkertijd zijn er externe prikkels van licht en geluid die bedoeld zijn om hen af te leiden. Als ze een fout maken, worden ze gestraft door een strenge leraar die stereotiepe uitspraken naar hen roept.

Als het te veel wordt voor de spelers, kunnen ze op een noodknop drukken om de simulatie te beëindigen.

## Installatie
### Benodigdheden
- 2 Raspberry Pi's + mini sd kaartjes
- RPI Pixel Matrix
- 4 kleine gekleurde knoppen (rood, geel, groen, blauw)
- 1 grote rode knop.
- stroboscoop
- power switch
- Network switch
- Philips Hub
- 2 philips hue lamps
- 2 philips LED strips.
- laptop
- tv of groot scherm.
- kabels (LAN, HDMI, etc)

### Software

Je kan deze repository in 1 keer downloaden en op de juiste plaatsen zetten met `` git clone git@github.com:gigano01/TeamTerror-TerrorRoom.git --recursive ``.
Maar je kan ook elke repository appart installeren op het apperaat.

#### Hoofd Pi

Installeer de repository met `` git clone git@github.com:gigano01/creativecoding_terrorroom_pi1.git ``. En voer `` npm install `` uit.
Hierna moet je de config aanpassen zodat het programma je lampen en hub kan vinden. Deze kan je vinden in 'config/devices.json'.
Hoe de API key en het IP adress te vinden kan je hier bekijken: [HUE dev manual](https://developers.meethue.com/develop/get-started-2/)

Stel de netwerkinstellingen in om deze pi het netwerkadress `` 192.168.100.1 `` te geven.

#### Matrix Pi

Installeer de repository met `` git clone git@github.com:gigano01/creativecoding_terrorroom_pi2.git --recursive ``. En voer `` npm install `` uit.
Hierna zal je in de 'rpi-matrix-pixelpusher' folder moeten gaan en `` make `` uitvoeren. Dit installeert het achtergrondprogramma die we nodig hebben om de matrix aan te sturen.

Stel de netwerkinstellingen in om deze pi het netwerkadress `` 192.168.100.3 `` te geven.

#### Laptop

Installeer de repository met `` git clone git@github.com:sammiadergent/creativecoding_terrorroom.git ``. En voer weer `` npm install `` uit.

#### Laatste stappen

Om het makkelijker te maken kan je een startup script maken voor de pi's. [Tutorial](https://www.dexterindustries.com/howto/run-a-program-on-your-raspberry-pi-at-startup/)

### Opstellen

Je moet een doos maken die groot genoeg is om de pi's te houden, de Matrix display goed kan vasthouden en de knoppen juist positioneerd.
Hieronder staat een voordbeeld van hoe het originele was gemaakt. **Belangerijk:** Zorg dat de stroboscoop geen interacties vormt met de knoppen. Dit was een probleem voor de originele installatie waardoor HSP modus onbruikbaar was. Zorg dat deze goed verdeeld zitten!

<img src="https://github.com/gigano01/TeamTerror-TerrorRoom/assets/28507122/d03907e1-88ff-4065-a2e9-7bb80ebdeaa0" width=50%>

Je zal ook de knoppen en stroboscoop in de juiste GPIO pinnen moeten steken van de Hoofd Pi.
| Type | GPIO pin |
| :----------- | :-----------: |
| noodStop  | 12 |
| knopBlauw | 21 |
| knopRood | 20 |
| knopGroen | 16 |
| knopGeel | 26 |
| flasher | 23 |

Hierna kan je alles aansluiten aan de network switch. Zorg dat je alle onderdelen tegelijkertijd aan kan zetten, dus twee/drie grote stekkerdozen met aan/uit knoppen zijn aangeraden.

### Starten van de installatie

Als je alle stappen hebt gevolgt zou je nu simpelweg de stekkerdozen moeten kunnen aanzetten om de installatie te starten. Het grote scherm moet je manueel aanzetten met `` npm run dev ``. Open de pagina op fullscreen, en klik op "start voor audio". Als de lichten niet langer rood zijn, is alles klaar.
je zal de pagina opnieuw moeten laden als de lichten eerder niet gedimd waren, hierbij moet je opniew op "start voor audio" klikken.

## Front-end 
Bij de start van het spel doorlopen alle spelers dezelfde introductieschermen. Hier ontvangen ze waarschuwingen, disclaimers en instructies voor het spelen van het spel. Na deze schermen kunnen ze kiezen tussen HSP, ADHD en ASS modus. De hoofdvideo en de achtergrondgeluiden zijn hetzelfde voor iedereen. Echter, de overprikkeling, gedachten en de uitspraken van de meester zijn specifiek voor elke modus. Wanneer spelers het spel beëindigen door op de noodknop te drukken, krijgen ze informatieve outro-schermen te zien. 

![Flowchart front-end](https://github.com/gigano01/TeamTerror-TerrorRoom/assets/136450680/de43d441-f632-4ae4-aa97-3d6b99dab98c)
### Connectie met websocket 
Om het frontend gedeelte met het backend gedeelte te verbinden, dat ervoor zorgt dat het spel werkt en de juiste schermen activeert, moet er eerst een verbinding met de websocket gemaakt worden.


**JS**
```
const socket = new WebSocket("ws://192.168.100.1:8080");

// Connection opened
socket.addEventListener("open", (event) => {
  // socket.send("Hello Server!");
});

// Connection closed
socket.addEventListener("close", (event) => {
  console.log("Server connection closed: ", event.code);
});

// Connection error
socket.addEventListener("error", (event) => {
  console.error("WebSocket error: ", event);
});
```
### Browser klaarmaken voor geluid 
Door een knop toe te voegen tijdens de eerste keer dat de browser opent, bereiden we de browser voor om geluid af te spelen wanneer nodig, zonder een muisklik van de gebruiker.

**HTML**
```
<button id="next">start voor audio</button>
```
**JS**
```
document.getElementById("next").addEventListener("click", function () {
  document.getElementById("next").classList.add("invisable");
});
```





### Intro schermen 
Elk scherm is in een div geplaatst met de class  ‘intro_(nummer scherm)’ en invisable’ met uitzondering voor het aller eerste scherm. 
Met de fysieke buttons wordt er door de verschillende intro’s gegaan.
De instructievideo wordt in een sequentie afgespeeld wanneer je bij het tweede introductiescherm komt, dit om de grootte van het bestand te beperken.
**HTML** 
```   <div class="intro_0 parent">
      <img src="../public/styling/achtergrond.jpeg" class="achtergrond"> 
      <img src="../public/styling/waarschuwing.svg" class="waarschuwing">
      <button id="next">start voor audio</button>
      <div class="h1">Waarschuwing</div>
      <div class="body">flitsende lichten <br> harde geluiden <br> Extreme overprikkeling</div>
      <div class="buttonsuitleg">
        <div class="h2">Klik op:</div>
        <div class="bolleparent">
          <div><img src="../public/styling/rodebol.svg" class="bol"></div>
          <div><img src="../public/styling/groenebol.svg" class="bol"></div>
          <div><img src="../public/styling/gelebol.svg" class="bol"></div>
        </div>
        <div class="h2">Om verder te gaan</div>
      </div>
    </div>
    <div class="intro_1 invisable parent">
      <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
      <img src="../public/styling/waarschuwing.svg" class="waarschuwing">
      <div class="h1">Disclaimer</div>
      <div class="grotestuktekst">
        <div class="body">De besproken gesimuleerde situaties zijn stereotypen en weerspiegelen niet ieders ervaringen met deze aandoeningen. De uitdagingen variëren per individu.</div>
      </div>
      <div class="buttonsuitleg">
        <div class="h2">Klik op:</div>
        <div class="bolleparent">
          <div><img src="../public/styling/rodebol.svg" class="bol"></div>
          <div><img src="../public/styling/groenebol.svg" class="bol"></div>
          <div><img src="../public/styling/gelebol.svg" class="bol"></div>
        </div>
        <div class="h2">Om verder te gaan</div>
      </div>
    </div>
    <div class="intro_2 invisable parent ">
      <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
      <div class="h1">Hoe speel je het spel?</div>
      <div class="body">Ken je het spel 'ik ga op reis en ik neem mee'? <br> Voeg telkens de laatst getoonde kleur toe aan de reeks</div>
      <img id="uitlegVideo" src="/instructie/2324_CC_TeamTerror_uitlegPNGs_0000.png">
      <div class="body">Riep de leerkracht? <br> begin opnieuw.</div>
      
      <div class="buttonsuitleg">
        <div class="h2">Klik op:</div>
        <div class="bolleparent">
          <div><img src="../public/styling/rodebol.svg" class="bol"></div>
          <div><img src="../public/styling/groenebol.svg" class="bol"></div>
          <div><img src="../public/styling/gelebol.svg" class="bol"></div>
        </div>
        <div class="h2">Om verder te gaan</div>
      </div>
    </div>
    <div class="intro_3 invisable parent">
      <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
      <img src="../public/styling/noodstop.svg" class="waarschuwing">
      <div class="h1">Noodstop</div>
      <div class="body">Lukt het niet meer? <br>Druk enkel dan op de grote rode knop</div>
      <div class="buttonsuitleg">
        <div class="h2">Klik op:</div>
        <div class="bolleparent">
          <div><img src="../public/styling/rodebol.svg" class="bol"></div>
          <div><img src="../public/styling/groenebol.svg" class="bol"></div>
          <div><img src="../public/styling/gelebol.svg" class="bol"></div>
        </div>
        <div class="h2">Om verder te gaan</div>
      </div>
    </div>
    <div class="intro_4  invisable parent">
      <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
      <div class="h1" id="locatie_1">Locatie</div>
      <div class="body">Lokaal 202<br>Geschiedenis<br>Populatie leerlingen 22</div>
      <div class="buttonsuitleg" id="buttonsuitleg_1">
        <div class="h2">Klik op:</div>
        <div class="bolleparent">
          <div><img src="../public/styling/rodebol.svg" class="bol"></div>
          <div><img src="../public/styling/groenebol.svg" class="bol"></div>
          <div><img src="../public/styling/gelebol.svg" class="bol"></div>
        </div>
        <div class="h2">Om verder te gaan</div>
      </div>
    </div>
    <div class="intro_5 invisable parent">
      <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
      <div class="h1">In welke student leef jij je in?</div>
      <div class="optiesmodus">
        <div class="option ass colorDiv1" id="colorDiv1">
          <div class="keuzeparent">
            <img src="../public/styling/rodebol.svg" class="keuzebol"> <div class="body left">ASS</div>
        </div>
        <div class="option hsp colorDiv3" id="colorDiv3">
          <div class="keuzeparent">
            <img src="../public/styling/groenebol.svg" class="keuzebol"> <div class="body left">Hooggevoeligheid</div>
          </div>
        </div>
        <div class="option adhd colorDiv2" id="colorDiv2">
          <div class="keuzeparent">
            <img src="../public/styling/gelebol.svg" class="keuzebol"> <div class="body left">ADHD</div>
          </div>
          <div class="h2 buttonsuitleg"> Druk 2x op een knop om het spel te starten</div>
        </div>
        </div>
    </div>
```


Bij de introductieschermen wordt er een verbinding gemaakt met de websocket. Deze voert een select message uit voor de eerste vijf clicks op welke knop dan ook. Deze actie activeert de intro-teller, waardoor de 'invisible' class aan de huidige div wordt toegevoegd en van de volgende wordt verwijderd.


**HTML** 
```
document.getElementById("next").addEventListener("click", function () {
  document.getElementById("next").classList.add("invisable");
});

// Listen for messages
socket.addEventListener("message", (event) => {
  const decodedMessage = JSON.parse(event.data);

  console.log("Message from server: ", decodedMessage);
  if (decodedMessage.type === "select") {
    introCounter++;
    if (introCounter > 0) {
      const prevElement = document.querySelector(`.intro_${introCounter - 1}`);
      if (prevElement) {
        prevElement.classList.add("invisable");
      }
    }

    const currentElement = document.querySelector(`.intro_${introCounter}`);
    if (currentElement) {
      currentElement.classList.remove("invisable");
    }

//Sequence
let images = [];
for (let i = 0; i <= 479; i++) {
  let paddedNumber = String(i).padStart(5, "0");
  images.push(`instructie/2324_CC_TeamTerror_uitlegPNGs_${paddedNumber}.png`);
}

let imgElement = document.getElementById("uitlegVideo");

let index = 0;
function changeImage() {
  if (introCounter === 2) {
    imgElement.src = images[index];
    index = (index + 1) % images.length; // Loop back to the first image when reaching the end
  }
}
setInterval(changeImage, 40);


```
### Modus keuze
Vanaf intro-scherm 5 verandert de boodschap van de websocket naar kleuren die verbonden zijn aan de verschillende spelmodi en beginnen we aan het spel.

Rood: ASS 

Groen: HSP

Geel: ADHD

**JS**
```
let gamemode = 0;
    console.log("introCounter: ", introCounter);
    if (introCounter === 6) {
      switch (decodedMessage.data.color) {
        case 2:
          gamemode = "ADHD";
          break;
        case 3:
          gamemode = "HSP";
          break;
        case 1:
          gamemode = "ASS";
      }
      console.log("gameMode: " + gamemode);
```

### Algemene elementen 
De algemene elementen worden voor elke modus afgespeeld en worden onderbroken als de speler een fout maakt. 

**HTML** 
```
<div class="video_main invisable" id="mainVideo">
      <video width="320" height="240" controls>
      <source src="/main_video.mp4" type="video/mp4">
      </video>
    </div>
```

**JS**
```
const videoElement = document.querySelector("#mainVideo");
const mainvideo = videoElement.querySelector("video");

 mainvideo.play();

```
### Opbouw modi
Voor elke modus is er een lijst van audiofragmenten, bestaande uit gedachtegeluiden en willekeurige klasgeluiden, die afwisselend uit de luidspreker komen wanneer de hoofdvideo wordt afgespeeld.

**JS**
```
//panner
const audioContext = new AudioContext();

function loadAudioFile(context, url) {
  return fetch(url)
    .then((response) => response.arrayBuffer())
    .then((arrayBuffer) => context.decodeAudioData(arrayBuffer));
}

function playSound(context, audioBuffer, panValue) {
  let source = context.createBufferSource();
  let panner = context.createStereoPanner();
  source.buffer = audioBuffer;
  source.connect(panner);
  panner.connect(context.destination);
  panner.pan.value = panValue;
  source.start();
}

loadAudioFile(audioContext, "audio1.m4a")
  .then((audioBuffer) => playSound(audioContext, audioBuffer, 1))
  .catch((error) => console.error(error));

// audio elementen
const gameModeAudios = {
  ADHD: [
    { file: loadAudioFile(audioContext, "adhd_1.m4a"), pan: -1 },
    { file: loadAudioFile(audioContext, "adhd_2.m4a"), pan: 1 },
    { file: loadAudioFile(audioContext, "adhd_3.m4a"), pan: -1 },
    { file: loadAudioFile(audioContext, "adhd_4.m4a"), pan: 1 },
    { file: loadAudioFile(audioContext, "random_1.mp3"), pan: -1 },
    { file: loadAudioFile(audioContext, "random_2.mp3"), pan: 1 },
    { file: loadAudioFile(audioContext, "random_3.mp3"), pan: -1 },
  ],
  HSP: [
    { file: loadAudioFile(audioContext, "hsp_1.m4a"), pan: -1 },
    { file: loadAudioFile(audioContext, "hsp_2.m4a"), pan: 1 },
    { file: loadAudioFile(audioContext, "hsp_3.m4a"), pan: -1 },
    { file: loadAudioFile(audioContext, "hsp_4.m4a"), pan: 1 },
    { file: loadAudioFile(audioContext, "random_1.mp3"), pan: -1 },
    { file: loadAudioFile(audioContext, "random_2.mp3"), pan: 1 },
    { file: loadAudioFile(audioContext, "random_3.mp3"), pan: -1 },
  ],
  ASS: [
    { file: loadAudioFile(audioContext, "ass_1.m4a"), pan: -1 },
    { file: loadAudioFile(audioContext, "ass_2.m4a"), pan: 1 },
    { file: loadAudioFile(audioContext, "ass_3.m4a"), pan: -1 },
    { file: loadAudioFile(audioContext, "random_1.mp3"), pan: 1 },
    { file: loadAudioFile(audioContext, "random_2.mp3"), pan: -1 },
    { file: loadAudioFile(audioContext, "random_3.mp3"), pan: 1 },
  ],
};

//geluiden afspelen
      setInterval(() => {
        if (!mainvideo.paused) {
          const audioFiles = gameModeAudios[gamemode];
          const audioFile =
            audioFiles[Math.floor(Math.random() * audioFiles.length)];

          console.log(`Game mode: ${gamemode}, Audio file: ${audioFile}`);
          console.log(audioFile);

          audioFile.file
            .then((audioBuffer) => {
              // Once the audio buffer is loaded, play the sound
              playSound(audioContext, audioBuffer, audioFile.pan);
            })
            .catch((error) => {
              console.error("Error loading audio file:", error);
            });
        }
      }, 5000);
    }
  }
```
Als er een fout wordt gemaakt in het spel, zal de websocket een "fail" signaal versturen dat de juiste game-mode activeert. Deze zorgt ervoor dat de juiste "jumpscare" wordt getoond, waarna de hoofdvideo verder afspeelt. De video's worden op de volgende manier in de html geplaatst.

**HTML**
```
<div class="video_ass_jumpscare_1 invisable" id="failvideo1_1">
          <video width="320" height="240" controls>
            <source src="/jumpscare_ass1.mp4" type="video/mp4">
            </video>
        </div>
        <div class="video_ass_jumpscare_2 invisable" id="failvideo1_2" >
          <video width="320" height="240" controls>
            <source src="/jumpscare_ass2.mp4" type="video/mp4">
            </video>
        </div>
        <div class="video_ass_jumpscare_3 invisable" id="failvideo1_3">
          <video width="320" height="240" controls>
            <source src="/jumpscare_ass4.mp4" type="video/mp4">
            </video>
        </div>
```
**JS**
```
else if (decodedMessage.type === "fail") {
    console.log("JUMPSCARE");
    if (gamemode === "ADHD") {
      mainvideo.pause();
      videoElement.classList.add("invisable");

      const jumpscareElement = document.querySelector(
        `#failvideo2_${videocounter}`,
      );
      const jumpVideo = jumpscareElement.querySelector("video");

      if (jumpVideo) {
        jumpVideo.play();
        console.log("Parent div ID: " + jumpVideo.parentNode.id);
        videocounter++;
        if (videocounter >= 8) {
          videocounter = 1;
        }

        jumpVideo.addEventListener("ended", () => {
          jumpscareElement.classList.add("invisable");
          videoElement.classList.remove("invisable");
          mainvideo.play();
        });
      }

      jumpscareElement.classList.remove("invisable");
    }
    if (gamemode === "ASS") {
      mainvideo.pause();
      videoElement.classList.add("invisable");

      const jumpscareElement = document.querySelector(
        `#failvideo1_${videocounter}`,
      );
      const jumpVideo = jumpscareElement.querySelector("video");

      if (jumpVideo) {
        jumpVideo.play();
        console.log("Parent div ID: " + jumpVideo.parentNode.id);
        videocounter++;
        if (videocounter >= 4) {
          videocounter = 1;
        } // Increment videocounter here

        jumpVideo.addEventListener("ended", () => {
          jumpscareElement.classList.add("invisable");
          videoElement.classList.remove("invisable");
          //mainvideo.muted = true;
          mainvideo.play();
        });
      }

      jumpscareElement.classList.remove("invisable");
    }
    console.log("JUMPSCARE");
    if (gamemode === "HSP") {
      mainvideo.pause();
      videoElement.classList.add("invisable");

      const jumpscareElement = document.querySelector(
        `#failvideo3_${videocounter}`,
      );
      const jumpVideo = jumpscareElement.querySelector("video");

      if (jumpVideo) {
        jumpVideo.play();
        console.log("Parent div ID: " + jumpVideo.parentNode.id);
        videocounter++;
        if (videocounter >= 9) {
          videocounter = 1;
        }

        jumpVideo.addEventListener("ended", () => {
          jumpscareElement.classList.add("invisable");
          videoElement.classList.remove("invisable");
          mainvideo.play();
        });
      }
      jumpscareElement.classList.remove("invisable");
    }
  }
```
### Noodstop en outro 
Wanneer de noodstop wordt geactiveerd, wordt er een stopsignaal via de websocket geactiveerd dat de noodstop-pagina activeert. Vervolgens wordt er een signaal ontvangen dat 'oselect' wordt genoemd, waardoor er door de outro-pagina's kan worden geklikt op dezelfde manier als de intro.

**HTML**
```
  <div class="outro_1 invisable parent">
    <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
    <div class="uitleggrid">
      <div class="uitlegcontent">
        <div><img src="../public/styling/mensengeel.svg" class="mensen"></div>
        <div class="body">5% tot 7% <br>kinderen lijdt aan ADHD</div>
      </div>
      <div class="uitlegcontent">
        <div><img src="../public/styling/mensenrood.svg" class="mensen"></div>
        <div class="body">3% <br> bevindt zich op het ASS-spectrum</div>
      </div>
      <div class="uitlegcontent">
        <div><img src="../public/styling/mensengroen.svg" class="mensen"></div>
        <div class="body">15% tot 20% <br>is hooggevoelig</div>
      </div>
    </div>
    <div class="buttonsuitleg" id="buttonsuitleg_2">
      <div class="h2">Klik op:</div>
      <div class="bolleparent">
        <div><img src="../public/styling/rodebol.svg" class="bol"></div>
        <div><img src="../public/styling/groenebol.svg" class="bol"></div>
        <div><img src="../public/styling/gelebol.svg" class="bol"></div>
      </div>
      <div class="h2">Om verder te gaan</div>
    </div>
  </div>
<div class="outro_2 invisable parent ">
  <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
  <div class="body grotestuktekst">
    Veel kinderen die pas als volwassenen ontdekken dat zij bij deze cijfers horen, ervaren vooroordelen en misverstanden. Ze zijn unieke individuen met eigen talenten, niet hun diagnose. Een begripvolle omgeving kan het verschil maken. 
Begin het gesprek. 
Zij zijn onze toekomst.
  </div>
  <div class="buttonsuitleg" id="buttonsuitleg_3">
    <div class="h2">Klik op:</div>
    <div class="bolleparent">
      <div><img src="../public/styling/rodebol.svg" class="bol"></div>
      <div><img src="../public/styling/groenebol.svg" class="bol"></div>
      <div><img src="../public/styling/gelebol.svg" class="bol"></div>
    </div>
    <div class="h2">Om verder te gaan</div>
  </div>
</div>
</div>
<div class="outro_3 invisable">
  <img src="../public/styling/achtergrond.jpeg" class="achtergrond">
</div>
</div>
```
**JS**
```
let outroCounter = 0;

else if (decodedMessage.type === "stop") {
    mainvideo.pause();
    mainvideo.currentTime = 0;
    console.log(mainvideo.currentTime);
    videoElement.classList.add("invisable");
    const noodstop = document.querySelector(`.noodstop`);
    noodstop.classList.remove("invisable");
  } else if (decodedMessage.type === "oselect") {
    const noodstop = document.querySelector(`.noodstop`);
    const outro_1 = document.querySelector(`.outro_1`);
    noodstop.classList.add("invisable");
    outro_1.classList.remove("invisable");
    outroCounter++;
    if (outroCounter > 0) {
      const prevElement = document.querySelector(`.outro_${outroCounter - 1}`);
      if (prevElement) {
        prevElement.classList.add("invisable");
      }
    }
    const currentElement = document.querySelector(`.outro_${outroCounter}`);
    if (currentElement) {
      currentElement.classList.remove("invisable");
    }
```
### Reset 
Zodra de outro-teller is afgelopen, wordt alles gereset na het laatste scherm en kan het spel opnieuw worden gespeeld.

**JS**
```
if (outroCounter === 3) {
      console.log("hier komt hij in de select state");
      const outro1 = document.querySelector(`.outro_1`);
      outro1.classList.add("invisable");
      setTimeout(() => {
        outroCounter = 0;
        introCounter = 0;
        gamemode = 0;
        videocounter = 1;
        const outro_3 = document.querySelector(`.outro_3`);
        const intro = document.querySelector(`.intro_0`);
        const outro1 = document.querySelector(`.outro_1`);
        outro1.classList.add("invisable");
        outro_3.classList.add("invisable");
        intro.classList.remove("invisable");
        console.log("hier zou hij moeten resetten");
      }, 0);
    }
```
## visitekaartjes 
De visitekaartjes hebben een voor- en achterkant. Omdat er aan beide zijden spot-uv is gebruikt, moet je de kaartjes dubbel vouwen. Hierdoor worden ze extra stevig.

**Druk**
<img width="1282" alt="Scherm­afbeelding 2024-05-28 om 14 17 08" src="https://github.com/gigano01/TeamTerror-TerrorRoom/assets/136450680/8f651374-122c-4e89-9ec8-6606b0dd921e">
**Cut**
<img width="1282" alt="Scherm­afbeelding 2024-05-28 om 14 17 31" src="https://github.com/gigano01/TeamTerror-TerrorRoom/assets/136450680/1e889f99-e042-4bc6-a372-015392af8cbb">














































