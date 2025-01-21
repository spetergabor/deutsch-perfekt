<template>
  <div class="app">
  <div class="verb-practice">
    <!-- SVG kör számláló -->
    <div class="progress-container">
      <svg width="100" height="100" viewBox="0 0 100 100">
        <circle cx="50" cy="50" r="45" fill="none" stroke="#ddd" stroke-width="3" />
        <circle
          cx="50"
          cy="50"
          r="45"
          fill="none"
          stroke="#4caf50"
          stroke-width="3"
          stroke-dasharray="282.6"
          :stroke-dashoffset="circleDashOffset"
        />
        <text x="50%" y="50%" text-anchor="middle" stroke-width="1px" dy=".3em" font-size="16">
          {{ currentRoundQuestionIndex }}
        </text>
      </svg>
    </div>

    <h1>Német igék gyakorlása</h1>
    <p id="kerdes">
      Kérdés: <strong :style="{ color: '#009cde' }">{{ currentQuestion.verb }}</strong><br>
      Add meg a segédigét és a múlt idejű alakot!<br> Jelentése: <em>{{ currentQuestion.meaning }}</em>
    </p>
    <input
      type="text"
      v-model="userAnswer"
      placeholder="Írd be a választ (pl. ist gegangen)"
      :disabled="isAnswered"
      :class="inputClass"
      @keyup.enter="checkAnswer"
    />
    <button @click="checkAnswer" :disabled="isAnswered || !userAnswer.trim()">Ellenőrzés</button>
    <p 
  class="feedbackbox" 
  v-if="feedback" 
  :style="{ backgroundColor: isCorrect ? '#28c114' : '#ff4d4d', color: 'white' }"
>
  {{ feedback }}
</p>

<!-- Popup ablak a statisztikához -->
<div v-if="showStatistics" class="popup-overlay">
  <div class="popup-content">
    <h2>Statisztika</h2>
    <p style="margin-bottom: 5px;">Helyes válaszok: {{ correctAnswers }}</p>
    <p style="margin-top: 5px; margin-bottom: 5px;">Helytelen válaszok: {{ incorrectAnswers }}</p>
    <ul>
      <li v-for="(verb, index) in solvedVerbs" :key="index" :style="{ color: verb.isCorrect ? 'green' : 'red' }">
        {{ verb.verb }} - 
        <span>
          Te válaszod: {{ verb.userAnswer }}
        </span> - 
        <span>
          Helyes válasz: {{ verb.correctAnswer }}
        </span>
      </li>
    </ul>

    <button @click="continueGame" :disabled="incorrectAnswers > 0">Folytatás</button>
    <button id="resetbutton" @click="resetGame">Újrakezdés ugyanazokkal az igékkel</button>
  </div>
</div>

    
  </div>
</div>
</template>

<script>
export default {
  name: "VerbPractice",
  data() {
    return {
      verbs: [
      { verb: "anfangen", auxiliary: "hat", pastParticiple: "angefangen", meaning: "kezdeni" },
  { verb: "ankommen", auxiliary: "ist", pastParticiple: "angekommen", meaning: "megérkezni" },
  { verb: "anrufen", auxiliary: "hat", pastParticiple: "angerufen", meaning: "felhívni" },
  { verb: "aufstehen", auxiliary: "ist", pastParticiple: "aufgestanden", meaning: "felkelni" },
  { verb: "bekommen", auxiliary: "hat", pastParticiple: "bekommen", meaning: "kapni" },
  { verb: "beginnen", auxiliary: "hat", pastParticiple: "begonnen", meaning: "elkezdeni" },
  { verb: "bleiben", auxiliary: "ist", pastParticiple: "geblieben", meaning: "maradni" },
  { verb: "bringen", auxiliary: "hat", pastParticiple: "gebracht", meaning: "hozni" },
  { verb: "denken", auxiliary: "hat", pastParticiple: "gedacht", meaning: "gondolni" },
  { verb: "dürfen", auxiliary: "hat", pastParticiple: "gedurft", meaning: "szabad, lehet (engedély)" },
  { verb: "essen", auxiliary: "hat", pastParticiple: "gegessen", meaning: "enni" },
  { verb: "fahren", auxiliary: "ist", pastParticiple: "gefahren", meaning: "utazni, vezetni" },
  { verb: "fangen", auxiliary: "hat", pastParticiple: "gefangen", meaning: "elkapni" },
  { verb: "fernsehen", auxiliary: "hat", pastParticiple: "ferngesehen", meaning: "tévét nézni" },
  { verb: "finden", auxiliary: "hat", pastParticiple: "gefunden", meaning: "találni" },
  { verb: "fliegen", auxiliary: "ist", pastParticiple: "geflogen", meaning: "repülni" },
  { verb: "geben", auxiliary: "hat", pastParticiple: "gegeben", meaning: "adni" },
  { verb: "gehen", auxiliary: "ist", pastParticiple: "gegangen", meaning: "menni" },
  { verb: "haben", auxiliary: "hat", pastParticiple: "gehabt", meaning: "birtokolni, van neki" },
  { verb: "heißen", auxiliary: "hat", pastParticiple: "geheißen", meaning: "hívni (nevén szólítani)" },
  { verb: "helfen", auxiliary: "hat", pastParticiple: "geholfen", meaning: "segíteni" },
  { verb: "kennen", auxiliary: "hat", pastParticiple: "gekannt", meaning: "ismerni" },
  { verb: "kommen", auxiliary: "ist", pastParticiple: "gekommen", meaning: "jönni" },
  { verb: "können", auxiliary: "hat", pastParticiple: "gekonnt", meaning: "tudni, képes lenni" },
  { verb: "lesen", auxiliary: "hat", pastParticiple: "gelesen", meaning: "olvasni" },
  { verb: "mögen", auxiliary: "hat", pastParticiple: "gemocht", meaning: "kedvelni, szeretni" },
  { verb: "müssen", auxiliary: "hat", pastParticiple: "gemusst", meaning: "kelleni, muszáj" },
  { verb: "nehmen", auxiliary: "hat", pastParticiple: "genommen", meaning: "venni, elvenni" },
  { verb: "rufen", auxiliary: "hat", pastParticiple: "gerufen", meaning: "hívni, szólítani" },
  { verb: "schlafen", auxiliary: "hat", pastParticiple: "geschlafen", meaning: "aludni" },
  { verb: "schreiben", auxiliary: "hat", pastParticiple: "geschrieben", meaning: "írni" },
  { verb: "schwimmen", auxiliary: "ist", pastParticiple: "geschwommen", meaning: "úszni" },
  { verb: "sehen", auxiliary: "hat", pastParticiple: "gesehen", meaning: "látni" },
  { verb: "sein", auxiliary: "ist", pastParticiple: "gewesen", meaning: "lenni" },
  { verb: "singen", auxiliary: "hat", pastParticiple: "gesungen", meaning: "énekelni" },
  { verb: "sollen", auxiliary: "hat", pastParticiple: "gesollt", meaning: "kelleni (javasolt)" },
  { verb: "sprechen", auxiliary: "hat", pastParticiple: "gesprochen", meaning: "beszélni" },
  { verb: "stehen", auxiliary: "hat", pastParticiple: "gestanden", meaning: "állni" },
  { verb: "treffen", auxiliary: "hat", pastParticiple: "getroffen", meaning: "találkozni" },
  { verb: "trinken", auxiliary: "hat", pastParticiple: "getrunken", meaning: "inni" },
  { verb: "tun", auxiliary: "hat", pastParticiple: "getan", meaning: "csinálni, tenni" },
  { verb: "verstehen", auxiliary: "hat", pastParticiple: "verstanden", meaning: "érteni" },
  { verb: "wehtun", auxiliary: "hat", pastParticiple: "wehgetan", meaning: "fájni" },
  { verb: "wissen", auxiliary: "hat", pastParticiple: "gewusst", meaning: "tudni (információt)" },
  { verb: "wollen", auxiliary: "hat", pastParticiple: "gewollt", meaning: "akarni" },
  { verb: "abbiegen", auxiliary: "ist", pastParticiple: "abgebogen", meaning: "bekanyarodni" },
  { verb: "abfahren", auxiliary: "ist", pastParticiple: "abgefahren", meaning: "elindulni (járművel)" },
  { verb: "abfliegen", auxiliary: "ist", pastParticiple: "abgeflogen", meaning: "felszállni (repülővel)" },
  { verb: "abgeben", auxiliary: "hat", pastParticiple: "abgegeben", meaning: "leadni" },
  { verb: "abwaschen", auxiliary: "hat", pastParticiple: "abgewaschen", meaning: "elmosni" },
  { verb: "anbieten", auxiliary: "hat", pastParticiple: "angeboten", meaning: "kínálni" },
  { verb: "(sich) anziehen", auxiliary: "hat", pastParticiple: "angezogen", meaning: "felöltözni" },
  { verb: "aufladen", auxiliary: "hat", pastParticiple: "aufgeladen", meaning: "feltölteni (pl. telefont)" },
  { verb: "aufwachsen", auxiliary: "ist", pastParticiple: "aufgewachsen", meaning: "felnőni" },
  { verb: "ausgeben", auxiliary: "hat", pastParticiple: "ausgegeben", meaning: "költeni (pénzt)" },
  { verb: "ausschlafen", auxiliary: "hat", pastParticiple: "ausgeschlafen", meaning: "kialussza magát" },
  { verb: "aussehen", auxiliary: "hat", pastParticiple: "ausgesehen", meaning: "kinéz (valahogy)" },
  { verb: "aussprechen", auxiliary: "hat", pastParticiple: "ausgesprochen", meaning: "kifejez" },
  { verb: "aussteigen", auxiliary: "ist", pastParticiple: "ausgestiegen", meaning: "kiszáll" },
  { verb: "(sich) ausziehen", auxiliary: "hat", pastParticiple: "ausgezogen", meaning: "levetkőzik" },
  { verb: "sich befinden", auxiliary: "hat", pastParticiple: "befunden", meaning: "található, elhelyezkedik" },
  { verb: "beschreiben", auxiliary: "hat", pastParticiple: "beschrieben", meaning: "leír" },
  { verb: "biegen", auxiliary: "hat", pastParticiple: "gebogen", meaning: "hajlít" },
  { verb: "bieten", auxiliary: "hat", pastParticiple: "geboten", meaning: "kínál" },
  { verb: "bitten", auxiliary: "hat", pastParticiple: "gebeten", meaning: "kér" },
  { verb: "braten", auxiliary: "hat", pastParticiple: "gebraten", meaning: "süt (olajban/zsírban)" },
  { verb: "einladen", auxiliary: "hat", pastParticiple: "eingeladen", meaning: "meghív" },
  { verb: "einschlafen", auxiliary: "ist", pastParticiple: "eingeschlafen", meaning: "elalszik" },
  { verb: "einsteigen", auxiliary: "ist", pastParticiple: "eingestiegen", meaning: "beszáll" },
  { verb: "einziehen", auxiliary: "ist", pastParticiple: "eingezogen", meaning: "beköltözik" },
  { verb: "wachsen", auxiliary: "ist", pastParticiple: "gewachsen", meaning: "nő" },
  { verb: "(sich) waschen", auxiliary: "hat", pastParticiple: "gewaschen", meaning: "mosakodik" },
  { verb: "werden", auxiliary: "ist", pastParticiple: "geworden", meaning: "lesz" },
  { verb: "werfen", auxiliary: "hat", pastParticiple: "geworfen", meaning: "dob" },
  { verb: "wiedergeben", auxiliary: "hat", pastParticiple: "wiedergegeben", meaning: "visszaad" },
  { verb: "ziehen", auxiliary: "hat", pastParticiple: "gezogen", meaning: "húz" },
  { verb: "zurückfahren", auxiliary: "ist", pastParticiple: "zurückgefahren", meaning: "visszautazik" },
  { verb: "zurückgeben", auxiliary: "hat", pastParticiple: "zurückgegeben", meaning: "visszaad" },
  { verb: "zurückkommen", auxiliary: "ist", pastParticiple: "zurückgekommen", meaning: "visszajön" },
  { verb: "abbrechen", auxiliary: "hat", pastParticiple: "abgebrochen", meaning: "félbeszakít" },
  { verb: "abhängen", auxiliary: "hat", pastParticiple: "abgehangen", meaning: "függ valamitől" },
  { verb: "ablaufen", auxiliary: "ist", pastParticiple: "abgelaufen", meaning: "lejár" },
  { verb: "abnehmen", auxiliary: "hat", pastParticiple: "abgenommen", meaning: "lefogy, csökken" },
  { verb: "abschließen", auxiliary: "hat", pastParticiple: "abgeschlossen", meaning: "lezár" },
  { verb: "abschneiden", auxiliary: "hat", pastParticiple: "abgeschnitten", meaning: "levág" },
  { verb: "abschreiben", auxiliary: "hat", pastParticiple: "abgeschrieben", meaning: "leír" },
  { verb: "absenden", auxiliary: "hat", pastParticiple: "abgesendet", meaning: "elküld" },
  { verb: "absinken", auxiliary: "ist", pastParticiple: "abgesunken", meaning: "lesüllyed" },
  { verb: "absteigen", auxiliary: "ist", pastParticiple: "abgestiegen", meaning: "leszáll" },
  { verb: "angeben", auxiliary: "hat", pastParticiple: "angegeben", meaning: "megad" },
  { verb: "angreifen", auxiliary: "hat", pastParticiple: "angegriffen", meaning: "megtámad" },
  { verb: "anhalten", auxiliary: "hat", pastParticiple: "angehalten", meaning: "megáll" },
  { verb: "anlügen", auxiliary: "hat", pastParticiple: "angelogen", meaning: "hazudik valakinek" },
  { verb: "annehmen", auxiliary: "hat", pastParticiple: "angenommen", meaning: "elfogad" },
  { verb: "ansehen", auxiliary: "hat", pastParticiple: "angeschaut", meaning: "megnéz" },
  { verb: "ansprechen", auxiliary: "hat", pastParticiple: "angesprochen", meaning: "megszólít" },
  { verb: "ansteigen", auxiliary: "ist", pastParticiple: "angestiegen", meaning: "emelkedik" },
  { verb: "anwachsen", auxiliary: "ist", pastParticiple: "angewachsen", meaning: "megnő" },
  { verb: "anwenden", auxiliary: "hat", pastParticiple: "angewendet", meaning: "alkalmaz" },
  { verb: "aufbleiben", auxiliary: "ist", pastParticiple: "aufgeblieben", meaning: "ébrent marad" },
  { verb: "auffallen", auxiliary: "ist", pastParticiple: "aufgefallen", meaning: "feltűnik" },
  { verb: "aufgeben", auxiliary: "hat", pastParticiple: "aufgegeben", meaning: "felad" },
  { verb: "aufgehen", auxiliary: "ist", pastParticiple: "aufgegangen", meaning: "felkel (nap)" },
  { verb: "aufhalten", auxiliary: "hat", pastParticiple: "aufgehalten", meaning: "feltart" },
  { verb: "auflassen", auxiliary: "hat", pastParticiple: "aufgelassen", meaning: "nyitva hagy" },
  { verb: "aufnehmen", auxiliary: "hat", pastParticiple: "aufgenommen", meaning: "felvesz (valamit)" },
  { verb: "aufrufen", auxiliary: "hat", pastParticiple: "aufgerufen", meaning: "felszólít" },
  { verb: "aufschieben", auxiliary: "hat", pastParticiple: "aufgeschoben", meaning: "elhalaszt" },
  { verb: "aufschlagen", auxiliary: "hat", pastParticiple: "aufgeschlagen", meaning: "felnyit" },
  { verb: "aufschließen", auxiliary: "hat", pastParticiple: "aufgeschlossen", meaning: "kinyit (kulccsal)" },
  { verb: "aufschneiden", auxiliary: "hat", pastParticiple: "aufgeschnitten", meaning: "felvág" },
  { verb: "aufschreiben", auxiliary: "hat", pastParticiple: "aufgeschrieben", meaning: "felír" },
  { verb: "auftreten", auxiliary: "ist", pastParticiple: "aufgetreten", meaning: "fellép (valahol)" },
  { verb: "ausdenken", auxiliary: "hat", pastParticiple: "ausgedacht", meaning: "kitalál" },
  { verb: "ausfallen", auxiliary: "ist", pastParticiple: "ausgefallen", meaning: "kiesik, elmarad" },
  { verb: "ausgehen", auxiliary: "ist", pastParticiple: "ausgegangen", meaning: "kimegy, szórakozni megy" },
  { verb: "aushalten", auxiliary: "hat", pastParticiple: "ausgehalten", meaning: "elvisel" },
  { verb: "auskennen", auxiliary: "hat", pastParticiple: "ausgekannt", meaning: "eligazodik" },
  { verb: "ausladen", auxiliary: "hat", pastParticiple: "ausgeladen", meaning: "kirak, kipakol" },
  { verb: "ausleihen", auxiliary: "hat", pastParticiple: "ausgeliehen", meaning: "kölcsönöz" },
  { verb: "ausrufen", auxiliary: "hat", pastParticiple: "ausgerufen", meaning: "kihirdet" },
  { verb: "aussterben", auxiliary: "ist", pastParticiple: "ausgestorben", meaning: "kihal" },
  { verb: "austrinken", auxiliary: "hat", pastParticiple: "ausgetrunken", meaning: "kiiszik" },
  { verb: "befehlen", auxiliary: "hat", pastParticiple: "befohlen", meaning: "parancsol" },
  { verb: "begraben", auxiliary: "hat", pastParticiple: "begraben", meaning: "eltemet" },
  { verb: "begreifen", auxiliary: "hat", pastParticiple: "begriffen", meaning: "felfog, megért" },
  { verb: "behalten", auxiliary: "hat", pastParticiple: "behalten", meaning: "megtart" },
  { verb: "beibringen", auxiliary: "hat", pastParticiple: "beigebracht", meaning: "megtanít" },
  { verb: "beißen", auxiliary: "hat", pastParticiple: "gebissen", meaning: "harap" },
  { verb: "beitragen", auxiliary: "hat", pastParticiple: "beigetragen", meaning: "hozzájárul" },
  { verb: "belügen", auxiliary: "hat", pastParticiple: "belogen", meaning: "hazudik valakinek" },
  { verb: "benehmen", auxiliary: "hat", pastParticiple: "benommen", meaning: "viselkedik" },
  { verb: "beraten", auxiliary: "hat", pastParticiple: "beraten", meaning: "tanácsol" },
  { verb: "beschließen", auxiliary: "hat", pastParticiple: "beschlossen", meaning: "elhatároz" },
  { verb: "besitzen", auxiliary: "hat", pastParticiple: "besessen", meaning: "birtokol" },
  { verb: "besprechen", auxiliary: "hat", pastParticiple: "besprochen", meaning: "megbeszél" },
  { verb: "bestehen", auxiliary: "hat", pastParticiple: "bestanden", meaning: "helytáll, fennáll" },
  { verb: "betragen", auxiliary: "hat", pastParticiple: "betragen", meaning: "kitesz (összeget)" },
  { verb: "betreffen", auxiliary: "hat", pastParticiple: "betroffen", meaning: "érint (valamit)" },
  { verb: "betreten", auxiliary: "hat", pastParticiple: "betreten", meaning: "belép" },
  { verb: "betrügen", auxiliary: "hat", pastParticiple: "betrogen", meaning: "becsap" },
  { verb: "beweisen", auxiliary: "hat", pastParticiple: "bewiesen", meaning: "bebizonyít" },
  { verb: "bewerben", auxiliary: "hat", pastParticiple: "beworben", meaning: "pályázik" },
  { verb: "beziehen", auxiliary: "hat", pastParticiple: "bezogen", meaning: "kapcsolódik" },
  { verb: "binden", auxiliary: "hat", pastParticiple: "gebunden", meaning: "köt" },
  { verb: "brechen", auxiliary: "hat", pastParticiple: "gebrochen", meaning: "eltör" },
  { verb: "brennen", auxiliary: "hat", pastParticiple: "gebrannt", meaning: "ég" },
  { verb: "durchfallen", auxiliary: "ist", pastParticiple: "durchgefallen", meaning: "megbukik" },
  { verb: "durchlesen", auxiliary: "hat", pastParticiple: "durchgelesen", meaning: "átolvas" },
  { verb: "durchschlafen", auxiliary: "hat", pastParticiple: "durchgeschlafen", meaning: "átalszik" },
  { verb: "durchschneiden", auxiliary: "hat", pastParticiple: "durchgeschnitten", meaning: "átvág" },
  { verb: "einbrechen", auxiliary: "ist", pastParticiple: "eingebrochen", meaning: "betör" },
  { verb: "einfallen", auxiliary: "ist", pastParticiple: "eingefallen", meaning: "eszébe jut" },
  { verb: "eingeben", auxiliary: "hat", pastParticiple: "eingegeben", meaning: "beír, bevitel" },
  { verb: "eingießen", auxiliary: "hat", pastParticiple: "eingegossen", meaning: "beleönt, önt" },
  { verb: "einnehmen", auxiliary: "hat", pastParticiple: "eingenommen", meaning: "bevesz (gyógyszert), elfoglal" },
  { verb: "einreiben", auxiliary: "hat", pastParticiple: "eingerieben", meaning: "bedörzsöl" },
  { verb: "einschreiben", auxiliary: "hat", pastParticiple: "eingeschrieben", meaning: "feliratkozik, beír" },
  { verb: "eintragen", auxiliary: "hat", pastParticiple: "eingetragen", meaning: "bejegyez" },
  { verb: "eintreffen", auxiliary: "ist", pastParticiple: "eingetroffen", meaning: "megérkezik" },
  { verb: "eintreten", auxiliary: "ist", pastParticiple: "eingetreten", meaning: "belép" },
  { verb: "empfehlen", auxiliary: "hat", pastParticiple: "empfohlen", meaning: "ajánl" },
  { verb: "empfinden", auxiliary: "hat", pastParticiple: "empfunden", meaning: "érez" },
  { verb: "entlassen", auxiliary: "hat", pastParticiple: "entlassen", meaning: "elbocsát" },
  { verb: "entscheiden", auxiliary: "hat", pastParticiple: "entschieden", meaning: "dönt" },
  { verb: "entstehen", auxiliary: "ist", pastParticiple: "entstanden", meaning: "keletkezik, létrejön" },
  { verb: "erbrechen", auxiliary: "hat", pastParticiple: "erbrochen", meaning: "hány" },
  { verb: "erfahren", auxiliary: "hat", pastParticiple: "erfahren", meaning: "megérkezik, megtud" },
  { verb: "ergeben", auxiliary: "hat", pastParticiple: "ergeben", meaning: "eredményez" },
  { verb: "erraten", auxiliary: "hat", pastParticiple: "erraten", meaning: "kitalál" },
  { verb: "erschießen", auxiliary: "hat", pastParticiple: "erschossen", meaning: "agyonlő" },
  { verb: "erschrecken", auxiliary: "ist", pastParticiple: "erschrocken", meaning: "megijed" },
  { verb: "ertrinken", auxiliary: "ist", pastParticiple: "ertrunken", meaning: "megfullad" },
  { verb: "erziehen", auxiliary: "hat", pastParticiple: "erzogen", meaning: "nevel" },
  { verb: "festhalten", auxiliary: "hat", pastParticiple: "festgehalten", meaning: "megtart" },
  { verb: "festnehmen", auxiliary: "hat", pastParticiple: "festgenommen", meaning: "letartóztat" },
  { verb: "feststehen", auxiliary: "hat", pastParticiple: "festgestanden", meaning: "bizonyos, biztos" },
  { verb: "fliehen", auxiliary: "ist", pastParticiple: "geflohen", meaning: "elmenekül" },
  { verb: "fließen", auxiliary: "ist", pastParticiple: "geflossen", meaning: "folyik" },
  { verb: "fortfahren", auxiliary: "hat", pastParticiple: "fortgefahren", meaning: "továbbmegy" },
  { verb: "fortziehen", auxiliary: "ist", pastParticiple: "fortgezogen", meaning: "elköltözik" },
  { verb: "freihalten", auxiliary: "hat", pastParticiple: "freigehalten", meaning: "szabadon tart" },
  { verb: "fressen", auxiliary: "hat", pastParticiple: "gefressen", meaning: "felfal (állat)" },
  { verb: "frieren", auxiliary: "hat", pastParticiple: "gefroren", meaning: "fázik, fagy" },
  { verb: "gelingen", auxiliary: "ist", pastParticiple: "gelungen", meaning: "sikerül" },
  { verb: "gelten", auxiliary: "hat", pastParticiple: "gegolten", meaning: "érvényes" },
  { verb: "genießen", auxiliary: "hat", pastParticiple: "genossen", meaning: "élvez" },
  { verb: "geschehen", auxiliary: "ist", pastParticiple: "geschehen", meaning: "történik" },
  { verb: "gestehen", auxiliary: "hat", pastParticiple: "gestanden", meaning: "bevall" },
  { verb: "gießen", auxiliary: "hat", pastParticiple: "gegossen", meaning: "önt" },
  { verb: "gleichen", auxiliary: "hat", pastParticiple: "geglichen", meaning: "hasonlít" },
  { verb: "graben", auxiliary: "hat", pastParticiple: "gegraben", meaning: "ás" },
  { verb: "greifen", auxiliary: "hat", pastParticiple: "gegriffen", meaning: "megragad" },
  { verb: "halten", auxiliary: "hat", pastParticiple: "gehalten", meaning: "tart" },
  { verb: "hängen", auxiliary: "hat", pastParticiple: "gehangen", meaning: "függ" },
  { verb: "heben", auxiliary: "hat", pastParticiple: "gehoben", meaning: "emel" },
  { verb: "herausfinden", auxiliary: "hat", pastParticiple: "herausgefunden", meaning: "rájön, megtud" },
  { verb: "herauskommen", auxiliary: "ist", pastParticiple: "herausgekommen", meaning: "kihoz" },
  { verb: "herausnehmen", auxiliary: "hat", pastParticiple: "herausgenommen", meaning: "kiemel" },
  { verb: "hereinbitten", auxiliary: "hat", pastParticiple: "hereingebeten", meaning: "behív" },
  { verb: "herunterkommen", auxiliary: "ist", pastParticiple: "heruntergekommen", meaning: "lejön" },
  { verb: "herunterladen", auxiliary: "hat", pastParticiple: "heruntergeladen", meaning: "letölteni" },
{ verb: "hervorheben", auxiliary: "hat", pastParticiple: "hervorgehoben", meaning: "kiemelni" },
{ verb: "hinabsehen", auxiliary: "hat", pastParticiple: "hinabgesehen", meaning: "lenézni" },
{ verb: "hinausgehen", auxiliary: "ist", pastParticiple: "hinausgegangen", meaning: "kimenni" },
{ verb: "hinbekommen", auxiliary: "hat", pastParticiple: "hinbekommen", meaning: "megoldani" },
{ verb: "hinfallen", auxiliary: "ist", pastParticiple: "hingefallen", meaning: "leesni" },
{ verb: "hingehen", auxiliary: "ist", pastParticiple: "hingegangen", meaning: "elmenni" },
{ verb: "hinkommen", auxiliary: "ist", pastParticiple: "hingekommen", meaning: "odaérni" },
{ verb: "hinuntersehen", auxiliary: "hat", pastParticiple: "hinuntergesehen", meaning: "lefelé nézni" },
{ verb: "hinweisen", auxiliary: "hat", pastParticiple: "hingewiesen", meaning: "rámutatni" },
{ verb: "hochgehen", auxiliary: "ist", pastParticiple: "hochgegangen", meaning: "felmenni" },
{ verb: "klingen", auxiliary: "hat", pastParticiple: "geklungen", meaning: "hangzani" },
{ verb: "lassen", auxiliary: "hat", pastParticiple: "gelassen", meaning: "hagyni" },
{ verb: "leiden", auxiliary: "hat", pastParticiple: "gelitten", meaning: "szenvedni" },
{ verb: "leihen", auxiliary: "hat", pastParticiple: "geliehen", meaning: "kölcsönadni" },
{ verb: "liebgewinnen", auxiliary: "hat", pastParticiple: "liebgewonnen", meaning: "megszeretni" },
{ verb: "lügen", auxiliary: "hat", pastParticiple: "gelogen", meaning: "hazudni" },
{ verb: "meiden", auxiliary: "hat", pastParticiple: "gemieden", meaning: "kerülni" },
{ verb: "messen", auxiliary: "hat", pastParticiple: "gemessen", meaning: "mérni" },
{ verb: "missfallen", auxiliary: "hat", pastParticiple: "missfallen", meaning: "nem tetszeni" },
{ verb: "misslingen", auxiliary: "ist", pastParticiple: "misslungen", meaning: "meghiusulni" },
{ verb: "mitgeben", auxiliary: "hat", pastParticiple: "mitgegeben", meaning: "odaadni" },
{ verb: "mitschreiben", auxiliary: "hat", pastParticiple: "mitgeschrieben", meaning: "leírni, másolni" },
{ verb: "mitsprechen", auxiliary: "hat", pastParticiple: "mitgesprochen", meaning: "beszélni valakivel" },
{ verb: "nachgeben", auxiliary: "hat", pastParticiple: "nachgegeben", meaning: "engedni" },
{ verb: "nachgehen", auxiliary: "ist", pastParticiple: "nachgegangen", meaning: "valami után menni" },
{ verb: "nachkommen", auxiliary: "ist", pastParticiple: "nachgekommen", meaning: "követni" },
{ verb: "nachlesen", auxiliary: "hat", pastParticiple: "nachgelesen", meaning: "újra elolvasni" },
{ verb: "nachschlagen", auxiliary: "hat", pastParticiple: "nachgeschlagen", meaning: "utánanézni" },
{ verb: "nachsehen", auxiliary: "hat", pastParticiple: "nachgesehen", meaning: "megnézni" },
{ verb: "nachsprechen", auxiliary: "hat", pastParticiple: "nachgesprochen", meaning: "megismételni" },
{ verb: "näherkommen", auxiliary: "ist", pastParticiple: "nähergekommen", meaning: "közelebb jönni" },
{ verb: "nähertreten", auxiliary: "ist", pastParticiple: "nähergetreten", meaning: "közelebb lépni" },
{ verb: "raten", auxiliary: "hat", pastParticiple: "geraten", meaning: "találgatni, tanácsolni" },
{ verb: "reiben", auxiliary: "hat", pastParticiple: "gerieben", meaning: "dörzsölni" },
{ verb: "reiten", auxiliary: "ist", pastParticiple: "geritten", meaning: "lovagolni" },
{ verb: "schaffen", auxiliary: "hat", pastParticiple: "geschaffen", meaning: "megteremteni, elérni" },
{ verb: "scheiden", auxiliary: "ist", pastParticiple: "geschieden", meaning: "elválni" },
{ verb: "scheinen", auxiliary: "hat", pastParticiple: "geschienen", meaning: "tűnni, világítani" },
{ verb: "scheißen", auxiliary: "hat", pastParticiple: "geschissen", meaning: "szarni" },
{ verb: "schieben", auxiliary: "hat", pastParticiple: "geschoben", meaning: "tolni" },
{ verb: "schießen", auxiliary: "hat", pastParticiple: "geschossen", meaning: "lőni" },
{ verb: "schlagen", auxiliary: "hat", pastParticiple: "geschlagen", meaning: "ütni" },
{ verb: "schmeißen", auxiliary: "hat", pastParticiple: "geschmissen", meaning: "dobni" },
{ verb: "schneiden", auxiliary: "hat", pastParticiple: "geschnitten", meaning: "vágni" },
{ verb: "schreien", auxiliary: "hat", pastParticiple: "geschrien", meaning: "kiabálni" },
{ verb: "senden", auxiliary: "hat", pastParticiple: "gesendet", meaning: "küldeni" },
{ verb: "sinken", auxiliary: "ist", pastParticiple: "gesunken", meaning: "süllyedni" },
{ verb: "springen", auxiliary: "ist", pastParticiple: "gesprungen", meaning: "ugrani" },
{ verb: "stehlen", auxiliary: "hat", pastParticiple: "gestohlen", meaning: "lopni" },
{ verb: "stinken", auxiliary: "hat", pastParticiple: "gestunken", meaning: "bűzleni" },
{ verb: "streiten", auxiliary: "hat", pastParticiple: "gestritten", meaning: "veszekedni" },
{ verb: "tragen", auxiliary: "hat", pastParticiple: "getragen", meaning: "hordani" },
{ verb: "treten", auxiliary: "ist", pastParticiple: "getreten", meaning: "rúgni, lépni" },
{ verb: "trügen", auxiliary: "hat", pastParticiple: "getrogen", meaning: "meghazudtolni" },
{ verb: "übelnehmen", auxiliary: "hat", pastParticiple: "übelgenommen", meaning: "rossz néven venni" },
{ verb: "überdenken", auxiliary: "hat", pastParticiple: "überdacht", meaning: "átgondolni" },
{ verb: "überfallen", auxiliary: "hat", pastParticiple: "überfallen", meaning: "megtámadni" },
{ verb: "überfliegen", auxiliary: "hat", pastParticiple: "überflogen", meaning: "átrepülni" },
{ verb: "übernehmen", auxiliary: "hat", pastParticiple: "übernommen", meaning: "átvenni" },
{ verb: "übersehen", auxiliary: "hat", pastParticiple: "übersehen", meaning: "nem észrevenni" },
{ verb: "übertreiben", auxiliary: "hat", pastParticiple: "übertrieben", meaning: "túlzásba vinni" },
{ verb: "überweisen", auxiliary: "hat", pastParticiple: "überwiesen", meaning: "átutalni" },
{ verb: "überwiegen", auxiliary: "hat", pastParticiple: "überwogen", meaning: "túlsúlyban lenni" },
{ verb: "umbringen", auxiliary: "hat", pastParticiple: "umgebracht", meaning: "megölni" },
{ verb: "umfallen", auxiliary: "ist", pastParticiple: "umgefallen", meaning: "leesni" },
{ verb: "unterbrechen", auxiliary: "hat", pastParticiple: "unterbrochen", meaning: "megszakítani" },
{ verb: "unterbringen", auxiliary: "hat", pastParticiple: "untergebracht", meaning: "elhelyezni" },
{ verb: "untergehen", auxiliary: "ist", pastParticiple: "untergegangen", meaning: "elmenni, lemenni" },
{ verb: "unterhalten", auxiliary: "hat", pastParticiple: "unterhalten", meaning: "szórakoztatni" },
{ verb: "unterlassen", auxiliary: "hat", pastParticiple: "unterlassen", meaning: "elmulasztani" },
{ verb: "unternehmen", auxiliary: "hat", pastParticiple: "unternommen", meaning: "vállalkozni" },
{ verb: "unterscheiden", auxiliary: "hat", pastParticiple: "unterschieden", meaning: "megkülönböztetni" },
{ verb: "untertreiben", auxiliary: "hat", pastParticiple: "untertrieben", meaning: "alulbecsülni" },
{ verb: "verbrennen", auxiliary: "hat", pastParticiple: "verbrannt", meaning: "megéget" },
  { verb: "verbringen", auxiliary: "hat", pastParticiple: "verbracht", meaning: "eltölteni (időt)" },
  { verb: "verfahren", auxiliary: "hat", pastParticiple: "verfahren", meaning: "tévedni, eltévedni" },
  { verb: "vergeben", auxiliary: "hat", pastParticiple: "vergeben", meaning: "megbocsátani" },
  { verb: "vergleichen", auxiliary: "hat", pastParticiple: "verglichen", meaning: "összehasonlítani" },
  { verb: "verhalten", auxiliary: "hat", pastParticiple: "verhalten", meaning: "viselkedni" },
  { verb: "verlassen", auxiliary: "hat", pastParticiple: "verlassen", meaning: "elhagyni" },
  { verb: "vermeiden", auxiliary: "hat", pastParticiple: "vermeiden", meaning: "elkerülni" },
  { verb: "verschieben", auxiliary: "hat", pastParticiple: "verschoben", meaning: "elhalasztani" },
  { verb: "verschließen", auxiliary: "hat", pastParticiple: "verschlossen", meaning: "bezárni" },
  { verb: "versprechen", auxiliary: "hat", pastParticiple: "versprochen", meaning: "megígérni" },
  { verb: "vertreten", auxiliary: "hat", pastParticiple: "vertreten", meaning: "helyettesíteni" },
  { verb: "vertun", auxiliary: "hat", pastParticiple: "vertan", meaning: "pazarlás" },
  { verb: "verzeihen", auxiliary: "hat", pastParticiple: "verziehen", meaning: "megbocsátani" },
  { verb: "vorbeigehen", auxiliary: "ist", pastParticiple: "vorbeigegangen", meaning: "elmenni, elhaladni" },
  { verb: "vorgehen", auxiliary: "ist", pastParticiple: "vorgegangen", meaning: "előrehaladni" },
  { verb: "vorhaben", auxiliary: "hat", pastParticiple: "vorgehabt", meaning: "tervezni" },
  { verb: "vorkommen", auxiliary: "ist", pastParticiple: "vorgekommen", meaning: "megtörténni, előfordulni" },
  { verb: "vorlesen", auxiliary: "hat", pastParticiple: "vorgelesen", meaning: "felolvasni" },
  { verb: "vorschlagen", auxiliary: "hat", pastParticiple: "vorgeschlagen", meaning: "javasolni" },
  { verb: "vortragen", auxiliary: "hat", pastParticiple: "vorgetragen", meaning: "előadni" },
  { verb: "vorziehen", auxiliary: "hat", pastParticiple: "vorgezogen", meaning: "előnyben részesíteni" },
  { verb: "wahrnehmen", auxiliary: "hat", pastParticiple: "wahrgenommen", meaning: "észrevenni, figyelembe venni" },
  { verb: "wegschmeißen", auxiliary: "hat", pastParticiple: "weggeschmissen", meaning: "eldobni" },
  { verb: "wegwerfen", auxiliary: "hat", pastParticiple: "weggeworfen", meaning: "eldobni" },
  { verb: "wegziehen", auxiliary: "ist", pastParticiple: "weggezogen", meaning: "elköltözni" },
  { verb: "weisen", auxiliary: "hat", pastParticiple: "gewiesen", meaning: "mutatni" },
  { verb: "weitergeben", auxiliary: "hat", pastParticiple: "weitergegeben", meaning: "továbbadni" },
  { verb: "weiterhelfen", auxiliary: "hat", pastParticiple: "weitergeholfen", meaning: "segíteni tovább" },
  { verb: "weiterkommen", auxiliary: "ist", pastParticiple: "weitergekommen", meaning: "továbbjutni" },
  { verb: "werben", auxiliary: "hat", pastParticiple: "geworben", meaning: "reklámozni" },
  { verb: "widersprechen", auxiliary: "hat", pastParticiple: "widersprochen", meaning: "ellentmondani" },
  { verb: "wiegen", auxiliary: "hat", pastParticiple: "gewogen", meaning: "mérni (súlyt)" },
  { verb: "zugeben", auxiliary: "hat", pastParticiple: "zugegeben", meaning: "elismerni" },
  { verb: "zugehen", auxiliary: "ist", pastParticiple: "zugegangen", meaning: "megközelíteni" },
  { verb: "zulassen", auxiliary: "hat", pastParticiple: "zugelassen", meaning: "engedélyezni" },
  { verb: "zunehmen", auxiliary: "hat", pastParticiple: "zugenommen", meaning: "növekedni, gyarapodni" },
  { verb: "zurechtfinden", auxiliary: "hat", pastParticiple: "zurechtgefunden", meaning: "megoldani, eligazodni" },
  { verb: "zurückbekommen", auxiliary: "hat", pastParticiple: "zurückbekommen", meaning: "visszakapni" },
  { verb: "zurückbleiben", auxiliary: "ist", pastParticiple: "zurückgeblieben", meaning: "hátramaradni" },
  { verb: "zurücklassen", auxiliary: "hat", pastParticiple: "zurückgelassen", meaning: "hátrahagyni" },
  { verb: "zurücknehmen", auxiliary: "hat", pastParticiple: "zurückgenommen", meaning: "visszavenni" },
  { verb: "zurückreiten", auxiliary: "ist", pastParticiple: "zurückgeritten", meaning: "visszarándulni" },
  { verb: "zurückziehen", auxiliary: "hat", pastParticiple: "zurückgezogen", meaning: "visszavonulni" },
  { verb: "zusammenhalten", auxiliary: "hat", pastParticiple: "zusammengehalten", meaning: "összetartani" },
  { verb: "zusammentun", auxiliary: "haben", pastParticiple: "zusammengetan", meaning: "összedobni" },
  { verb: "zusammenziehen", auxiliary: "haben", pastParticiple: "zusammengezogen", meaning: "összeköltözni" },
  { verb: "zuschließen", auxiliary: "hat", pastParticiple: "zugeschlossen", meaning: "bezárni" },
  { verb: "zutreffen", auxiliary: "hat", pastParticiple: "zugetroffen", meaning: "helyes, illik" },
  { verb: "zwingen", auxiliary: "hat", pastParticiple: "gezwungen", meaning: "kényszeríteni" }

      ],
      unansweredVerbs: [],
    solvedVerbs: [],
    currentQuestion: {},
    userAnswer: "",
    feedback: "",
    correctAnswers: 0,
    incorrectAnswers: 0,
    isAnswered: false,
    isCorrect: null,
    answersGiven: 0,
    showStatistics: false,
    totalVerbs: 0,
    answeredQuestions: 0,
    currentRoundQuestionIndex: 1, // Hányadik kérdésnél tartunk
    roundDetails: [], // Az adott kör részletes statisztikái
    currentRoundVerbs: [], // Új: az aktuális kör igéi
  };
  },
  computed: {
    // Számítja a stroke-dashoffset-et, hogy a kitöltött kör megfelelően változzon
    circleDashOffset() {
      const progress = (this.currentRoundQuestionIndex / 10) * 282.6;
      return 282.6 - progress;
    },
  },
  created() {
    this.resetUnansweredVerbs();
    this.totalVerbs = this.verbs.length;
    this.setRandomQuestion();
  },
  methods: {
    shuffleArray(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    },

    checkAnswer() {
  if (!this.userAnswer.trim()) {
    this.feedback = "Kérlek, add meg a választ!";
    return;
  }

  const correctAnswer = `${this.currentQuestion.auxiliary} ${this.currentQuestion.pastParticiple}`;
  this.isCorrect = this.userAnswer.trim().toLowerCase() === correctAnswer.toLowerCase();

  this.feedback = this.isCorrect
    ? "Helyes!"
    : `Helytelen! A helyes válasz: ${correctAnswer}`;

  // Növeljük a helyes vagy helytelen válaszok számát
  if (this.isCorrect) {
    this.correctAnswers++;
  } else {
    this.incorrectAnswers++;
  }

  // Hozzáadjuk a megoldott kérdést a solvedVerbs tömbhöz
  this.solvedVerbs.push({
    verb: this.currentQuestion.verb,
    userAnswer: this.userAnswer.trim(),
    correctAnswer,
    isCorrect: this.isCorrect,
  });

  this.isAnswered = true;
  this.answersGiven++;

  if (this.answersGiven >= 10) {
    this.showStatistics = true;
  } else {
    setTimeout(() => {
      this.nextQuestion();
    }, 3000);
  }
},

    nextQuestion() {
      if (this.unansweredVerbs.length > 0) {
        this.currentQuestion = this.unansweredVerbs.pop();
        this.userAnswer = "";
        this.feedback = "";
        this.isAnswered = false;
        this.isCorrect = null;
        this.currentRoundQuestionIndex++;
      } else {
        this.feedback = "Nincs több kérdés!";
      }
    },

    resetUnansweredVerbs() {
    // Az új körre mindig véletlenszerű sorrendben kapott igéket készít elő
    this.unansweredVerbs = this.shuffleArray([...this.verbs]);
    this.currentRoundVerbs = [...this.unansweredVerbs]; // Eltároljuk az aktuális kör igéit
  },

  resetGame() {
  this.answersGiven = 0;
  this.correctAnswers = 0;
  this.incorrectAnswers = 0;
  this.showStatistics = false;
  this.isAnswered = false;
  this.userAnswer = "";
  this.answeredQuestions = 0;
  this.currentRoundQuestionIndex = 1;

  this.unansweredVerbs = [...this.currentRoundVerbs];
  this.setRandomQuestion();
  this.feedback = "";
  this.isCorrect = null;
  this.solvedVerbs = []; // Megoldott igék törlése
},

continueGame() {
  // Teljes állapot visszaállítása alaphelyzetbe
  this.answersGiven = 0;
  this.correctAnswers = 0;
  this.incorrectAnswers = 0;
  this.isAnswered = false;
  this.userAnswer = "";
  this.feedback = "";
  this.isCorrect = null;
  this.currentRoundQuestionIndex = 1;
  this.solvedVerbs = [];
  this.showStatistics = false; // Popup bezárása

  // Az új kör indításához újra feltöltjük az unansweredVerbs tömböt véletlenszerű sorrendben
  this.resetUnansweredVerbs();
  this.setRandomQuestion(); // Az első kérdés beállítása
},

setRandomQuestion() {
  // Az igék sorrendje a `unansweredVerbs` alapján történik
  if (this.unansweredVerbs.length > 0) {
    this.currentQuestion = this.unansweredVerbs.pop(); // Az első kérdés az unansweredVerbs tömbből
  } else {
    this.feedback = "Nincs több kérdés!";
  }
},
  },
};
</script>


<style scoped>
hhtml, body {
  height: 100vh; /* Az oldal teljes magassága */
  margin: 0;
  display: flex;
  justify-content: center; /* Horizontális középre igazítás */
  align-items: center; /* Vertikális középre igazítás */
  background-color: #ff4141; /* Háttérszín */
}

h2 {
  font-size: 36px;
}

.app {
  display: flex;
  justify-content: center; /* Tartalom vízszintes középre helyezése */
  align-items: center; /* Tartalom függőleges középre helyezése */
  width: 100%;
  height: 100vh; /* Teljes magasság kitöltése */
  background-image: linear-gradient(to right top, #051937, #171228, #190a1a, #12040d, #000000);

}

.verb-practice {
  max-width: 600px; /* Maximális szélesség */
  width: 100%; /* Teljes szélesség */
  padding: 20px;
  box-sizing: border-box;
  text-align: center;
  color: white;
  border-radius: 15px; /* Lekerekített sarkok */
}

h1 {
  padding-bottom: 20px;
}


input, button, .counters {
  width: 350px;
  margin: 10px auto; /* Középre igazítja a gombokat */
  padding: 8px;
  font-size: 16px;
  border-radius: 15px;
  box-sizing: border-box;
  display: inline-block;
  /*box-shadow: #000000 4px 4px 0 0; */
  cursor: pointer;
}

input {
  border: 0px solid #ccc;
}

button {
  background-color: #28c114;
  color: white;
  border: none;
  cursor: pointer;
  display: block; /* Inline-blokk elemek */
  margin: 10px auto; /* Középre igazítás */
  padding: 8px;
  font-size: 16px;
  border-radius: 15px;
  box-sizing: border-box;
  /* box-shadow: #000000 4px 4px 0 0; */
}

#nextq {
  background-color: #009cde;
}

#kerdes {
  padding-bottom: 20px;
}

button:hover {
  background-color: #186a0d;
  transition: all 175ms cubic-bezier(0, 0, 1, 1);

}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.statistics {
  margin-top: 20px;
  font-size: 16px;
  font-weight: bold;
}

.image-container {
  margin-top: 20px;
  text-align: center;
}

img {
  max-width: 350px;
  height: auto;
  border-radius: 15px;
  /* box-shadow: #000000 4px 4px 0 0; */
  margin-bottom: 50px;

}

.reset-button:hover {
  background-color: #886400;
  transition: all 175ms cubic-bezier(0, 0, 1, 1);
}

.statistics {
  margin-top: 20px;
  font-size: 16px;
  font-weight: bold;
  display: block; /* Flexbox használata */
  justify-content: center; /* Középre igazítás */
  gap: 20px; /* Különbség a számok között */
}

.correct-answers,
.incorrect-answers {
  font-size: 16px; /* Nagyobb méret a számoknak */
}

.continue-button {
  background-color: #000000;
  color: white;
  border: none;
  border-radius: 15px;
  cursor: pointer;
  font-size: 16px;

}

.reset-button {
  background-color: #ecaf07;
}

.continue-button:hover,
.reset-button:hover {
  opacity: 0.9;
}

.button-container {
  flex-direction: row-reverse;
  display: flex; /* Flexbox aktiválása a gombok egymás mellé helyezéséhez */
  justify-content: space-between; /* Gombok közötti távolság */
  width: 350px; /* Azonos szélesség, mint az input mező */
  margin: 0 auto; /* Középre igazítás */
  margin-top: 20px; /* Távolság a tartalom felett */
  box-sizing: border-box; /* Padding és border beleszámít a szélességbe */
  gap: 10px;
}

.continue-button,
.reset-button {
  width: 48%; /* Gombok szélessége a tartály méretének ~fele */
  padding: 10px; /* Gombok belső térköze */
  font-size: 16px; /* Szövegméret */
  text-align: center; /* Szöveg középre igazítása */

}

.continue-button {
  background-color: #28c114;
}

#resetbutton {
  background-color: #ecaf07;
}

#resetbutton:hover {
  background-color: #a87d08;
}

.counters {
  background-color: #131313;
  display: flex;
  border-radius: 15px;
  justify-content: center; /* Középre igazítás */
  gap: 1rem; /* Távolság a számlálók között */
  align-items: center; /* Függőleges igazítás */
}

.popup {
  position: fixed;
  width: 350px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: #009cde;
  border-radius: 15px;
  padding: 20px;
  border: 0px solid #ccc;
  /* box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1); */
  z-index: 1000;
}

.popup-content {
  text-align: center;
  line-height: 1em;
}

.progress-container {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

svg {
}

circle {
  transition: stroke-dashoffset 0.3s ease;
}

p {
  font-size: 16px;
  font-weight: bold;
  line-height: 1.5em;
  margin-top: 10px;
}

text {
  font-size: 36px;
  fill: #ffffff;
  font-weight: bold;
}

.feedbackbox {
  display: flex;
  justify-content: center; /* Horizontális középre igazítás */
  align-items: center;    /* Vertikális középre igazítás */
  width: 350px;
  height: auto;           /* Magasság automatikus (ha szöveg változó) */
  padding: 30px 0px 30px 0px;
  border-radius: 15px;
  text-align: center;     /* Szöveg igazítása középre */
  margin: 0 auto;         /* Középre helyezés az oldal közepén */
  margin-top: 20px;
}

ul {
  list-style-type: none;
  text-align: left;
  font-size: 14px;
}

/* A popup háttér elmosása */
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5); /* Áttetsző sötét háttér */
  backdrop-filter: blur(5px); /* A blur effekt */
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999; /* A popupot a többi elem fölé helyezi */
}

/* A popup ablak stílusai */
.popup-content {
  background-color:#009cde;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  text-align: center;
}

</style>
