<template>
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
      (Add meg a segédigét és a múlt idejű alakot!)
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
     <div v-if="showStatistics" class="popup">
      <div class="popup-content">
        <h2>Statisztika</h2>
        <p>Helyes válaszok: {{ correctAnswers }}</p>
        <p>Helytelen válaszok: {{ incorrectAnswers }}</p>
        
        <h3>Részletes válaszok:</h3>
        <ul>
          <li 
            v-for="(verb, index) in solvedVerbs" 
            :key="index"
            :style="{ color: verb.isCorrect ? 'green' : 'red' }"
          >
            {{ verb.verb }} - 
            <span :style="{ color: verb.isCorrect ? 'green' : 'red' }">
              Te válaszod: {{ verb.userAnswer }} 
            </span> 
            - {{ verb.isCorrect ? 'Helyes' : 'Helytelen' }}
          </li>
        </ul>

        <button @click="continueGame" :disabled="incorrectAnswers > 0">Folytatás</button>
        <button @click="resetGame">Újrakezdés ugyanazokkal az igékkel</button>
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
      { verb: "anfangen", auxiliary: "hat", pastParticiple: "angefangen" },
{ verb: "ankommen", auxiliary: "ist", pastParticiple: "angekommen" },
{ verb: "anrufen", auxiliary: "hat", pastParticiple: "angerufen" },
{ verb: "aufstehen", auxiliary: "ist", pastParticiple: "aufgestanden" },
{ verb: "bekommen", auxiliary: "hat", pastParticiple: "bekommen" },
{ verb: "beginnen", auxiliary: "hat", pastParticiple: "begonnen" },
{ verb: "bleiben", auxiliary: "ist", pastParticiple: "geblieben" },
{ verb: "bringen", auxiliary: "hat", pastParticiple: "gebracht" },
{ verb: "denken", auxiliary: "hat", pastParticiple: "gedacht" },
{ verb: "dürfen", auxiliary: "hat", pastParticiple: "gedurft" },
{ verb: "essen", auxiliary: "hat", pastParticiple: "gegessen" },
{ verb: "fahren", auxiliary: "ist", pastParticiple: "gefahren" },
{ verb: "fangen", auxiliary: "hat", pastParticiple: "gefangen" },
{ verb: "fernsehen", auxiliary: "hat", pastParticiple: "ferngesehen" },
{ verb: "finden", auxiliary: "hat", pastParticiple: "gefunden" },
{ verb: "fliegen", auxiliary: "ist", pastParticiple: "geflogen" },
{ verb: "geben", auxiliary: "hat", pastParticiple: "gegeben" },
{ verb: "gehen", auxiliary: "ist", pastParticiple: "gegangen" },
{ verb: "haben", auxiliary: "hat", pastParticiple: "gehabt" },
{ verb: "heißen", auxiliary: "hat", pastParticiple: "geheißen" },
{ verb: "helfen", auxiliary: "hat", pastParticiple: "geholfen" },
{ verb: "kennen", auxiliary: "hat", pastParticiple: "gekannt" },
{ verb: "kommen", auxiliary: "ist", pastParticiple: "gekommen" },
{ verb: "können", auxiliary: "hat", pastParticiple: "gekonnt" },
{ verb: "lesen", auxiliary: "hat", pastParticiple: "gelesen" },
{ verb: "mögen", auxiliary: "hat", pastParticiple: "gemocht" },
{ verb: "müssen", auxiliary: "hat", pastParticiple: "gemusst" },
{ verb: "nehmen", auxiliary: "hat", pastParticiple: "genommen" },
{ verb: "rufen", auxiliary: "hat", pastParticiple: "gerufen" },
{ verb: "schlafen", auxiliary: "hat", pastParticiple: "geschlafen" },
{ verb: "schreiben", auxiliary: "hat", pastParticiple: "geschrieben" },
{ verb: "schwimmen", auxiliary: "ist", pastParticiple: "geschwommen" },
{ verb: "sehen", auxiliary: "hat", pastParticiple: "gesehen" },
{ verb: "sein", auxiliary: "ist", pastParticiple: "gewesen" },
{ verb: "singen", auxiliary: "hat", pastParticiple: "gesungen" },
{ verb: "sollen", auxiliary: "hat", pastParticiple: "gesollt" },
{ verb: "sprechen", auxiliary: "hat", pastParticiple: "gesprochen" },
{ verb: "stehen", auxiliary: "hat", pastParticiple: "gestanden" },
{ verb: "treffen", auxiliary: "hat", pastParticiple: "getroffen" },
{ verb: "trinken", auxiliary: "hat", pastParticiple: "getrunken" },
{ verb: "tun", auxiliary: "hat", pastParticiple: "getan" },
{ verb: "verstehen", auxiliary: "hat", pastParticiple: "verstanden" },
{ verb: "wehtun", auxiliary: "hat", pastParticiple: "wehgetan" },
{ verb: "wissen", auxiliary: "hat", pastParticiple: "gewusst" },
{ verb: "wollen", auxiliary: "hat", pastParticiple: "gewollt" },
{ verb: "abbiegen", auxiliary: "ist", pastParticiple: "abgebogen" },
{ verb: "abfahren", auxiliary: "ist", pastParticiple: "abgefahren" },
{ verb: "abfliegen", auxiliary: "ist", pastParticiple: "abgeflogen" },
{ verb: "abgeben", auxiliary: "hat", pastParticiple: "abgegeben" },
{ verb: "abwaschen", auxiliary: "hat", pastParticiple: "abgewaschen" },
{ verb: "anbieten", auxiliary: "hat", pastParticiple: "angeboten" },
{ verb: "(sich) anziehen", auxiliary: "hat", pastParticiple: "angezogen" },
{ verb: "aufladen", auxiliary: "hat", pastParticiple: "aufgeladen" },
{ verb: "aufwachsen", auxiliary: "ist", pastParticiple: "aufgewachsen" },
{ verb: "ausgeben", auxiliary: "hat", pastParticiple: "ausgegeben" },
{ verb: "ausschlafen", auxiliary: "hat", pastParticiple: "ausgeschlafen" },
{ verb: "aussehen", auxiliary: "hat", pastParticiple: "ausgesehen" },
{ verb: "aussprechen", auxiliary: "hat", pastParticiple: "ausgesprochen" },
{ verb: "aussteigen", auxiliary: "ist", pastParticiple: "ausgestiegen" },
{ verb: "(sich) ausziehen", auxiliary: "hat", pastParticiple: "ausgezogen" },
{ verb: "sich befinden", auxiliary: "hat", pastParticiple: "befunden" },
{ verb: "beschreiben", auxiliary: "hat", pastParticiple: "beschrieben" },
{ verb: "biegen", auxiliary: "hat", pastParticiple: "gebogen" },
{ verb: "bieten", auxiliary: "hat", pastParticiple: "geboten" },
{ verb: "bitten", auxiliary: "hat", pastParticiple: "gebeten" },
{ verb: "braten", auxiliary: "hat", pastParticiple: "gebraten" },
{ verb: "einladen", auxiliary: "hat", pastParticiple: "eingeladen" },
{ verb: "einschlafen", auxiliary: "ist", pastParticiple: "eingeschlafen" },
{ verb: "einsteigen", auxiliary: "ist", pastParticiple: "eingestiegen" },
{ verb: "einziehen", auxiliary: "ist", pastParticiple: "eingezogen" },
{ verb: "wachsen", auxiliary: "ist", pastParticiple: "gewachsen" },
{ verb: "(sich) waschen", auxiliary: "hat", pastParticiple: "gewaschen" },
{ verb: "werden", auxiliary: "ist", pastParticiple: "geworden" },
{ verb: "werfen", auxiliary: "hat", pastParticiple: "geworfen" },
{ verb: "wiedergeben", auxiliary: "hat", pastParticiple: "wiedergegeben" },
{ verb: "ziehen", auxiliary: "hat", pastParticiple: "gezogen" },
{ verb: "zurückfahren", auxiliary: "ist", pastParticiple: "zurückgefahren" },
{ verb: "zurückgeben", auxiliary: "hat", pastParticiple: "zurückgegeben" },
{ verb: "zurückkommen", auxiliary: "ist", pastParticiple: "zurückgekommen" },
  { verb: "abbrechen", auxiliary: "hat", pastParticiple: "abgebrochen" },
  { verb: "abhängen", auxiliary: "hat", pastParticiple: "abgehangen" },
  { verb: "ablaufen", auxiliary: "ist", pastParticiple: "abgelaufen" },
  { verb: "abnehmen", auxiliary: "hat", pastParticiple: "abgenommen" },
  { verb: "abschließen", auxiliary: "hat", pastParticiple: "abgeschlossen" },
  { verb: "abschneiden", auxiliary: "hat", pastParticiple: "abgeschnitten" },
  { verb: "abschreiben", auxiliary: "hat", pastParticiple: "abgeschrieben" },
  { verb: "absenden", auxiliary: "hat", pastParticiple: "abgesendet" },
  { verb: "absinken", auxiliary: "ist", pastParticiple: "abgesunken" },
  { verb: "absteigen",auxiliary: "ist", pastParticiple: "abgestiegen" },
  { verb: "angeben", auxiliary: "hat", pastParticiple: "angegeben" },
  { verb: "angreifen", auxiliary: "hat", pastParticiple: "angegriffen" },
  { verb: "anhalten", auxiliary: "hat", pastParticiple: "angehalten" },
  { verb: "anlügen", auxiliary: "hat", pastParticiple: "angelogen" },
  { verb: "annehmen", auxiliary: "hat", pastParticiple: "angenommen" },
  { verb: "ansehen", auxiliary: "hat", pastParticiple: "angeschaut" }, // Beachte: "ansehen" wird oft mit "anschauen" ersetzt
  { verb: "ansprechen", auxiliary: "hat", pastParticiple: "angesprochen" },
  { verb: "ansteigen", auxiliary: "ist", pastParticiple: "angestiegen" },
  { verb: "anwachsen", auxiliary: "ist", pastParticiple: "angewachsen" },
  { verb: "anwenden", auxiliary: "hat", pastParticiple: "angewendet" },
  { verb: "aufbleiben", auxiliary: "ist", pastParticiple: "aufgeblieben" },
  { verb: "auffallen", auxiliary: "ist", pastParticiple: "aufgefallen" },
  { verb: "aufgeben", auxiliary: "hat", pastParticiple: "aufgegeben" },
  { verb: "aufgehen", auxiliary: "ist", pastParticiple: "aufgegangen" },
  { verb: "aufhalten", auxiliary: "hat", pastParticiple: "aufgehalten" },
  { verb: "auflassen", auxiliary: "hat", pastParticiple: "aufgelassen" },
  { verb: "aufnehmen", auxiliary: "hat", pastParticiple: "aufgenommen" },
  { verb: "aufrufen", auxiliary: "hat", pastParticiple: "aufgerufen" },
  { verb: "aufschieben", auxiliary: "hat", pastParticiple: "aufgeschoben" },
  { verb: "aufschlagen", auxiliary: "hat", pastParticiple: "aufgeschlagen" },
  { verb: "aufschließen", auxiliary: "hat", pastParticiple: "aufgeschlossen" },
  { verb: "aufschneiden", auxiliary: "hat", pastParticiple: "aufgeschnitten" },
  { verb: "aufschreiben", auxiliary: "hat", pastParticiple: "aufgeschrieben" },
  { verb: "auftreten", auxiliary: "ist", pastParticiple: "aufgetreten" },
  { verb: "ausdenken", auxiliary: "hat", pastParticiple: "ausgedacht" },
  { verb: "ausfallen", auxiliary: "ist", pastParticiple: "ausgefallen" },
  { verb: "ausgehen", auxiliary: "ist", pastParticiple: "ausgegangen" },
  { verb: "aushalten", auxiliary: "hat", pastParticiple: "ausgehalten" },
  { verb: "auskennen", auxiliary: "hat", pastParticiple: "ausgekannt" },
  { verb: "ausladen", auxiliary: "hat", pastParticiple: "ausgeladen" },
  { verb: "ausleihen", auxiliary: "hat", pastParticiple: "ausgeliehen" },
  { verb: "ausrufen", auxiliary: "hat", pastParticiple: "ausgerufen" },
  { verb: "aussterben", auxiliary: "ist", pastParticiple: "ausgestorben" },
  { verb: "austrinken", auxiliary: "hat", pastParticiple: "ausgetrunken" },
  { verb: "befehlen", auxiliary: "hat", pastParticiple: "befohlen" },
  { verb: "begraben", auxiliary: "hat", pastParticiple: "begraben" },
  { verb: "begreifen", auxiliary: "hat", pastParticiple: "begriffen" },
  { verb: "behalten", auxiliary: "hat", pastParticiple: "behalten" },
  { verb: "beibringen", auxiliary: "hat", pastParticiple: "beigebracht" },
  { verb: "beißen", auxiliary: "hat", pastParticiple: "gebissen" },
  { verb: "beitragen", auxiliary: "hat", pastParticiple: "beigetragen" },
  { verb: "belügen", auxiliary: "hat", pastParticiple: "belogen" },
  { verb: "benehmen", auxiliary: "hat", pastParticiple: "benommen" },
  { verb: "beraten", auxiliary: "hat", pastParticiple: "beraten" },
  { verb: "beschließen", auxiliary: "hat", pastParticiple: "beschlossen" },
  { verb: "besitzen", auxiliary: "hat", pastParticiple: "besessen" },
  { verb: "besprechen", auxiliary: "hat", pastParticiple: "besprochen" },
  { verb: "bestehen", auxiliary: "hat", pastParticiple: "bestanden" },
  { verb: "betragen", auxiliary: "hat", pastParticiple: "betragen" },
  { verb: "betreffen", auxiliary: "hat", pastParticiple: "betroffen" },
  { verb: "betreten", auxiliary: "hat", pastParticiple: "betreten" },
  { verb: "betrügen", auxiliary: "hat", pastParticiple: "betrogen" },
  { verb: "beweisen", auxiliary: "hat", pastParticiple: "bewiesen" },
  { verb: "bewerben", auxiliary: "hat", pastParticiple: "beworben" },
  { verb: "beziehen", auxiliary: "hat", pastParticiple: "bezogen" },
  { verb: "binden", auxiliary: "hat", pastParticiple: "gebunden" },
  { verb: "brechen", auxiliary: "hat", pastParticiple: "gebrochen" },
  { verb: "brennen", auxiliary: "hat", pastParticiple: "gebrannt" },
  { verb: "durchfallen", auxiliary: "ist", pastParticiple: "durchgefallen" },
  { verb: "durchlesen", auxiliary: "hat", pastParticiple: "durchgelesen" },
  { verb: "durchschlafen", auxiliary: "hat", pastParticiple: "durchgeschlafen" },
  { verb: "durchschneiden", auxiliary: "hat", pastParticiple: "durchgeschnitten" },
  { verb: "einbrechen", auxiliary: "ist", pastParticiple: "eingebrochen" },
  { verb: "einfallen", auxiliary: "ist", pastParticiple: "eingefallen" },
  { verb: "eingeben", auxiliary: "hat", pastParticiple: "eingegeben" },
  { verb: "eingießen", auxiliary: "hat", pastParticiple: "eingegossen" },
  { verb: "einnehmen", auxiliary: "hat", pastParticiple: "eingenommen" },
  { verb: "einreiben", auxiliary: "hat", pastParticiple: "eingerieben" },
  { verb: "einschreiben", auxiliary: "hat", pastParticiple: "eingeschrieben" },
  { verb: "eintragen", auxiliary: "hat", pastParticiple: "eingetragen" },
  { verb: "eintreffen", auxiliary: "ist", pastParticiple: "eingetroffen" },
  { verb: "eintreten", auxiliary: "ist", pastParticiple: "eingetreten" },
  { verb: "empfehlen", auxiliary: "hat", pastParticiple: "empfohlen" },
  { verb: "empfinden", auxiliary: "hat", pastParticiple: "empfunden" },
  { verb: "entlassen", auxiliary: "hat", pastParticiple: "entlassen" },
  { verb: "entscheiden", auxiliary: "hat", pastParticiple: "entschieden" },
  { verb: "entstehen", auxiliary: "ist", pastParticiple: "entstanden" },
  { verb: "erbrechen", auxiliary: "hat", pastParticiple: "erbrochen" },
  { verb: "erfahren", auxiliary: "hat", pastParticiple: "erfahren" },
  { verb: "ergeben", auxiliary: "hat", pastParticiple: "ergeben" },
  { verb: "erraten", auxiliary: "hat", pastParticiple: "erraten" },
  { verb: "erschießen", auxiliary: "hat", pastParticiple: "erschossen" },
  { verb: "erschrecken", auxiliary: "ist", pastParticiple: "erschrocken" }, // *ist* als Hilfsverb
  { verb: "ertrinken", auxiliary: "ist", pastParticiple: "ertrunken" },
  { verb: "erziehen", auxiliary: "hat", pastParticiple: "erzogen" },
  { verb: "festhalten", auxiliary: "hat", pastParticiple: "festgehalten" },
  { verb: "festnehmen", auxiliary: "hat", pastParticiple: "festgenommen" },
  { verb: "feststehen", auxiliary: "hat", pastParticiple: "festgestanden" },
  { verb: "fliehen", auxiliary: "ist", pastParticiple: "geflohen" },
  { verb: "fließen", auxiliary: "ist", pastParticiple: "geflossen" },
  { verb: "fortfahren", auxiliary: "hat", pastParticiple: "fortgefahren" },
  { verb: "fortziehen", auxiliary: "ist", pastParticiple: "fortgezogen" },
  { verb: "freihalten", auxiliary: "hat", pastParticiple: "freigehalten" },
  { verb: "fressen", auxiliary: "hat", pastParticiple: "gefressen" },
  { verb: "frieren", auxiliary: "hat", pastParticiple: "gefroren" },
  { verb: "gelingen", auxiliary: "ist", pastParticiple: "gelungen" },
  { verb: "gelten", auxiliary: "hat", pastParticiple: "gegolten" },
  { verb: "genießen", auxiliary: "hat", pastParticiple: "genossen" },
  { verb: "geschehen", auxiliary: "ist", pastParticiple: "geschehen" },
  { verb: "gestehen", auxiliary: "hat", pastParticiple: "gestanden" },
  { verb: "gießen", auxiliary: "hat", pastParticiple: "gegossen" },
  { verb: "gleichen", auxiliary: "hat", pastParticiple: "geglichen" },
  { verb: "graben", auxiliary: "hat", pastParticiple: "gegraben" },
  { verb: "greifen", auxiliary: "hat", pastParticiple: "gegriffen" },
  { verb: "halten", auxiliary: "hat", pastParticiple: "gehalten" },
  { verb: "hängen", auxiliary: "hat", pastParticiple: "gehangen" },
  { verb: "heben", auxiliary: "hat", pastParticiple: "gehoben" },
  { verb: "herausfinden", auxiliary: "hat", pastParticiple: "herausgefunden" },
  { verb: "herauskommen", auxiliary: "ist", pastParticiple: "herausgekommen" },
  { verb: "herausnehmen", auxiliary: "hat", pastParticiple: "herausgenommen" },
  { verb: "hereinbitten", auxiliary: "hat", pastParticiple: "hereingebeten" },
  { verb: "herunterkommen", auxiliary: "ist", pastParticiple: "heruntergekommen" },
  { verb: "herunterladen", auxiliary: "hat", pastParticiple: "heruntergeladen" },
  { verb: "hervorheben", auxiliary: "hat", pastParticiple: "hervorgehoben" },
  { verb: "hinabsehen", auxiliary: "hat", pastParticiple: "hinabgesehen" },
  { verb: "hinausgehen", auxiliary: "ist", pastParticiple: "hinausgegangen" },
  { verb: "hinbekommen", auxiliary: "hat", pastParticiple: "hinbekommen" },
  { verb: "hinfallen", auxiliary: "ist", pastParticiple: "hingefallen" },
  { verb: "hingehen", auxiliary: "ist", pastParticiple: "hingegangen" },
  { verb: "hinkommen", auxiliary: "ist", pastParticiple: "hingekommen" },
  { verb: "hinuntersehen", auxiliary: "hat", pastParticiple: "hinuntergesehen" },
  { verb: "hinweisen", auxiliary: "hat", pastParticiple: "hingewiesen" },
  { verb: "hochgehen", auxiliary: "ist", pastParticiple: "hochgegangen" },
  { verb: "klingen", auxiliary: "hat", pastParticiple: "geklungen" },
  { verb: "lassen", auxiliary: "hat", pastParticiple: "gelassen" },
  { verb: "leiden", auxiliary: "hat", pastParticiple: "gelitten" },
  { verb: "leihen", auxiliary: "hat", pastParticiple: "geliehen" },
  { verb: "liebgewinnen", auxiliary: "hat", pastParticiple: "liebgewonnen" },
  { verb: "lügen", auxiliary: "hat", pastParticiple: "gelogen" },
  { verb: "meiden", auxiliary: "hat", pastParticiple: "gemieden" },
  { verb: "messen", auxiliary: "hat", pastParticiple: "gemessen" },
  { verb: "missfallen", auxiliary: "hat", pastParticiple: "missfallen" },
  { verb: "misslingen", auxiliary: "ist", pastParticiple: "misslungen" },
  { verb: "mitgeben", auxiliary: "hat", pastParticiple: "mitgegeben" },
  { verb: "mitschreiben", auxiliary: "hat", pastParticiple: "mitgeschrieben" },
  { verb: "mitsprechen", auxiliary: "hat", pastParticiple: "mitgesprochen" },
  { verb: "nachgeben", auxiliary: "hat", pastParticiple: "nachgegeben" },
  { verb: "nachgehen", auxiliary: "ist", pastParticiple: "nachgegangen" },
  { verb: "nachkommen", auxiliary: "ist", pastParticiple: "nachgekommen" },
  { verb: "nachlesen", auxiliary: "hat", pastParticiple: "nachgelesen" },
  { verb: "nachschlagen", auxiliary: "hat", pastParticiple: "nachgeschlagen" },
  { verb: "nachsehen", auxiliary: "hat", pastParticiple: "nachgesehen" },
  { verb: "nachsprechen", auxiliary: "hat", pastParticiple: "nachgesprochen" },
  { verb: "näherkommen", auxiliary: "ist", pastParticiple: "nähergekommen" },
  { verb: "nähertreten", auxiliary: "ist", pastParticiple: "nähergetreten" },
{ verb: "raten", auxiliary: "hat", pastParticiple: "geraten" },
{ verb: "reiben", auxiliary: "hat", pastParticiple: "gerieben" },
{ verb: "reiten", auxiliary: "ist", pastParticiple: "geritten" },
{ verb: "schaffen", auxiliary: "hat", pastParticiple: "geschaffen" },
{ verb: "scheiden", auxiliary: "ist", pastParticiple: "geschieden" },
{ verb: "scheinen", auxiliary: "hat", pastParticiple: "geschienen" },
{ verb: "scheißen", auxiliary: "hat", pastParticiple: "geschissen" },
{ verb: "schieben", auxiliary: "hat", pastParticiple: "geschoben" },
{ verb: "schießen", auxiliary: "hat", pastParticiple: "geschossen" },
{ verb: "schlagen", auxiliary: "hat", pastParticiple: "geschlagen" },
{ verb: "schmeißen", auxiliary: "hat", pastParticiple: "geschmissen" },
{ verb: "schneiden", auxiliary: "hat", pastParticiple: "geschnitten" },
{ verb: "schreien", auxiliary: "hat", pastParticiple: "geschrien" },
{ verb: "senden", auxiliary: "hat", pastParticiple: "gesendet" },
{ verb: "sinken", auxiliary: "ist", pastParticiple: "gesunken" },
{ verb: "springen", auxiliary: "ist", pastParticiple: "gesprungen" },
{ verb: "stehlen", auxiliary: "hat", pastParticiple: "gestohlen" },
{ verb: "stinken", auxiliary: "hat", pastParticiple: "gestunken" },
{ verb: "streiten", auxiliary: "hat", pastParticiple: "gestritten" },
{ verb: "tragen", auxiliary: "hat", pastParticiple: "getragen" },
{ verb: "treten", auxiliary: "ist", pastParticiple: "getreten" },
{ verb: "trügen", auxiliary: "hat", pastParticiple: "getrogen" },
{ verb: "übelnehmen", auxiliary: "hat", pastParticiple: "übelgenommen" },
{ verb: "überdenken", auxiliary: "hat", pastParticiple: "überdacht" },
{ verb: "überfallen", auxiliary: "hat", pastParticiple: "überfallen" },
{ verb: "überfliegen", auxiliary: "hat", pastParticiple: "überflogen" },
{ verb: "übernehmen", auxiliary: "hat", pastParticiple: "übernommen" },
{ verb: "übersehen", auxiliary: "hat", pastParticiple: "übersehen" },
{ verb: "übertreiben", auxiliary: "hat", pastParticiple: "übertrieben" },
{ verb: "überweisen", auxiliary: "hat", pastParticiple: "überwiesen" },
{ verb: "überwiegen", auxiliary: "hat", pastParticiple: "überwogen" },
{ verb: "umbringen", auxiliary: "hat", pastParticiple: "umgebracht" },
  { verb: "umfallen", auxiliary: "ist", pastParticiple: "umgefallen" },
  { verb: "unterbrechen", auxiliary: "hat", pastParticiple: "unterbrochen" },
  { verb: "unterbringen", auxiliary: "hat", pastParticiple: "untergebracht" },
  { verb: "untergehen", auxiliary: "ist", pastParticiple: "untergegangen" },
  { verb: "unterhalten", auxiliary: "hat", pastParticiple: "unterhalten" },
  { verb: "unterlassen", auxiliary: "hat", pastParticiple: "unterlassen" },
  { verb: "unternehmen", auxiliary: "hat", pastParticiple: "unternommen" },
  { verb: "unterscheiden", auxiliary: "hat", pastParticiple: "unterschieden" },
  { verb: "untertreiben", auxiliary: "hat", pastParticiple: "untertrieben" },
  { verb: "verbrennen", auxiliary: "hat", pastParticiple: "verbrannt" },
  { verb: "verbringen", auxiliary: "hat", pastParticiple: "verbracht" },
  { verb: "verfahren", auxiliary: "hat", pastParticiple: "verfahren" },
  { verb: "vergeben", auxiliary: "hat", pastParticiple: "vergeben" },
  { verb: "vergleichen", auxiliary: "hat", pastParticiple: "verglichen" },
  { verb: "verhalten", auxiliary: "hat", pastParticiple: "verhalten" },
  { verb: "verlassen", auxiliary: "hat", pastParticiple: "verlassen" },
  { verb: "vermeiden", auxiliary: "hat", pastParticiple: "vermeiden" },
  { verb: "verschieben", auxiliary: "hat", pastParticiple: "verschoben" },
  { verb: "verschließen", auxiliary: "hat", pastParticiple: "verschlossen" },
  { verb: "versprechen", auxiliary: "hat", pastParticiple: "versprochen" },
  { verb: "vertreten", auxiliary: "hat", pastParticiple: "vertreten" },
  { verb: "vertun", auxiliary: "hat", pastParticiple: "vertan" },
  { verb: "verzeihen", auxiliary: "hat", pastParticiple: "verziehen" },
  { verb: "vorbeigehen", auxiliary: "ist", pastParticiple: "vorbeigegangen" },
  { verb: "vorgehen", auxiliary: "ist", pastParticiple: "vorgegangen" },
  { verb: "vorhaben", auxiliary: "hat", pastParticiple: "vorgehabt" },
  { verb: "vorkommen", auxiliary: "ist", pastParticiple: "vorgekommen" },
  { verb: "vorlesen", auxiliary: "hat", pastParticiple: "vorgelesen" },
  { verb: "vorschlagen", auxiliary: "hat", pastParticiple: "vorgeschlagen" },
  { verb: "vortragen", auxiliary: "hat", pastParticiple: "vorgetragen" },
  { verb: "vorziehen", auxiliary: "hat", pastParticiple: "vorgezogen" },
  { verb: "wahrnehmen", auxiliary: "hat", pastParticiple: "wahrgenommen" },
  { verb: "wegschmeißen", auxiliary: "hat", pastParticiple: "weggeschmissen" },
  { verb: "wegwerfen", auxiliary: "hat", pastParticiple: "weggeworfen" },
  { verb: "wegziehen", auxiliary: "ist", pastParticiple: "weggezogen" },
  { verb: "weisen", auxiliary: "hat", pastParticiple: "gewiesen" },
  { verb: "weitergeben", auxiliary: "hat", pastParticiple: "weitergegeben" },
  { verb: "weiterhelfen", auxiliary: "hat", pastParticiple: "weitergeholfen" },
  { verb: "weiterkommen", auxiliary: "ist", pastParticiple: "weitergekommen" },
  { verb: "werben", auxiliary: "hat", pastParticiple: "geworben" },
  { verb: "widersprechen", auxiliary: "hat", pastParticiple: "widersprochen" },
  { verb: "wiegen", auxiliary: "hat", pastParticiple: "gewogen" },
  { verb: "zugeben", auxiliary: "hat", pastParticiple: "zugegeben" },
  { verb: "zugehen", auxiliary: "ist", pastParticiple: "zugegangen" },
  { verb: "zulassen", auxiliary: "hat", pastParticiple: "zugelassen" },
  { verb: "zunehmen", auxiliary: "hat", pastParticiple: "zugenommen" },
  { verb: "zurechtfinden", auxiliary: "hat", pastParticiple: "zurechtgefunden" },
  { verb: "zurückbekommen", auxiliary: "hat", pastParticiple: "zurückbekommen" },
  { verb: "zurückbleiben", auxiliary: "ist", pastParticiple: "zurückgeblieben" },
  { verb: "zurücklassen", auxiliary: "hat", pastParticiple: "zurückgelassen" },
  { verb: "zurücknehmen", auxiliary: "hat", pastParticiple: "zurückgenommen" },
  { verb: "zurückreiten", auxiliary: "ist", pastParticiple: "zurückgeritten" },
  { verb: "zurückziehen", auxiliary: "hat", pastParticiple: "zurückgezogen" },
  { verb: "zusammenhalten", auxiliary: "hat", pastParticiple: "zusammengehalten" },
  { verb: "zusammentun", auxiliary: "haben", pastParticiple: "zusammengetan" },
  { verb: "zusammenziehen", auxiliary: "haben", pastParticiple: "zusammengezogen" },
  { verb: "zuschließen", auxiliary: "hat", pastParticiple: "zugeschlossen" },
  { verb: "zutreffen", auxiliary: "hat", pastParticiple: "zugetroffen" },
  { verb: "zwingen", auxiliary: "hat", pastParticiple: "gezwungen" }

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

  if (this.isCorrect) {
    this.correctAnswers++;
  } else {
    this.incorrectAnswers++;
  }

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

  // Az újraindításkor a previous round igéit használjuk
  // Az unansweredVerbs-t ne keverjük újra, hanem használjuk az aktuális kör igéit
  this.unansweredVerbs = [...this.currentRoundVerbs]; // Ugyanazokkal az igékkel indítjuk újra
  this.setRandomQuestion(); // Beállítjuk az első kérdést
  this.feedback = "";
  this.isCorrect = null;
  this.roundDetails = []; // Előző kör statisztikáinak törlése

  // Állapotok törlése (ha szükséges)
  this.solvedVerbs = [];
},

    continueGame() {
      if (this.incorrectAnswers === 0) {
        this.answersGiven = 0;
        this.showStatistics = false;
        this.setRandomQuestion();
      } else {
        this.feedback = "Csak akkor folytathatod, ha minden válasz helyes!";
      }
    },

    setRandomQuestion() {
  // Az igék sorrendje a `unansweredVerbs` alapján történik
  if (this.unansweredVerbs.length > 0) {
    this.currentQuestion = this.unansweredVerbs.pop();  // Az első kérdés az unansweredVerbs tömbből
  } else {
    this.feedback = "Nincs több kérdés!";
  }
},


  },
};
</script>


<style scoped>
html, body {
  height: 100vh;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #ff4141;
}

.verb-practice {
  max-width: 100%;
  padding-top: 5%;
  width: 100%;
  height: 100vh;
  box-sizing: border-box;
  text-align: center;
  background-image: linear-gradient(to right top, #051937, #171228, #190a1a, #12040d, #000000);  /* box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1); */
  color: white;
  overflow: hidden;
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
</style>
