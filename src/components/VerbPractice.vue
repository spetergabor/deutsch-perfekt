<template>
  <div class="verb-practice">
    <h1>Német igék gyakorlása</h1>
    <p id="kerdes">Kérdés: <strong :style="{ color: '#009cde' }">{{ currentQuestion.verb }}</strong> (Add meg a segédigét és a múlt idejű alakot!)</p>    <input
      type="text"
      v-model="userAnswer"
      placeholder="Írd be a választ (pl. ist gegangen)"
      :disabled="isAnswered"
      :class="inputClass"
      @keyup.enter="checkAnswer"
    />
    <button @click="checkAnswer" :disabled="isAnswered || !userAnswer.trim()">Ellenőrzés</button>
    <button @click="resetGame" class="reset-button">Nullázás és újrakezdés</button>
    <p v-if="feedback">{{ feedback }}</p>
    <button id="nextq" @click="nextQuestion" v-if="feedback && unansweredVerbs.length > 0">Következő kérdés</button>

    <!-- Statisztika megjelenítése -->
    <div class="statistics">
  <p class="correct-answers" :style="{ color: correctAnswers > 0 ? 'green' : '' }">{{ correctAnswers }}</p>
  <p class="incorrect-answers" :style="{ color: incorrectAnswers > 0 ? 'red' : '' }">{{ incorrectAnswers }}</p>
</div>

    <!-- Kép mindig megjelenik, ha nincs válasz -->
    <div class="image-container">
      <img
        v-if="!isAnswered"
        src="https://static1.srcdn.com/wordpress/wp-content/uploads/2019/09/The-Rock-Raises-The-Peoples-Eyebrow.jpg?q=50&fit=crop&w=1140&h=&dpr=1.5"
        alt="Válaszra vár"
      />
      <img
        v-if="isCorrect === true"
        src="https://hips.hearstapps.com/hmg-prod/images/dwayne-johnson-attends-the-jumanji-the-next-level-uk-film-news-photo-1575726701.jpg?resize=640:*"
        alt="Helyes válasz"
        v-show="isAnswered"
      />
      <img
        v-if="isCorrect === false"
        src="https://variety.com/wp-content/uploads/2020/12/Dwayne_Johnson.png?w=970&h=563&crop=1"
        alt="Helytelen válasz"
        v-show="isAnswered"
      />
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
  { verb: "vortragen", auxiliary: "hat", pastParticiple: "vgetragen" },
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
    };
  },
  created() {
    this.resetUnansweredVerbs();
    this.setRandomQuestion();
  },
  methods: {
    resetUnansweredVerbs() {
      this.unansweredVerbs = [...this.verbs];
      this.solvedVerbs = [];
      this.correctAnswers = 0;
      this.incorrectAnswers = 0;
      this.feedback = "";
      this.userAnswer = "";
      this.isAnswered = false;
      this.isCorrect = null;
    },
    resetGame() {
      this.resetUnansweredVerbs();
      this.setRandomQuestion();
    },
    setRandomQuestion() {
      if (this.unansweredVerbs.length === 0) {
        this.feedback = "Minden igét megoldottál! Gratulálok!";
        this.isAnswered = true;
        return;
      }
      const randomIndex = Math.floor(Math.random() * this.unansweredVerbs.length);
      this.currentQuestion = this.unansweredVerbs.splice(randomIndex, 1)[0];
      this.userAnswer = "";
      this.feedback = "";
      this.isAnswered = false;
      this.isCorrect = null;
    },
    checkAnswer() {
      if (!this.userAnswer.trim()) {
        this.feedback = "Kérlek, add meg a választ!";
        return;
      }

      const correctAnswer = `${this.currentQuestion.auxiliary} ${this.currentQuestion.pastParticiple}`;

      if (this.userAnswer.trim().toLowerCase() === correctAnswer.toLowerCase()) {
        this.feedback = "Helyes!";
        this.correctAnswers++;
        this.isCorrect = true;
        this.solvedVerbs.push(this.currentQuestion);
      } else {
        this.feedback = `Helytelen! A helyes válasz: ${correctAnswer}`;
        this.incorrectAnswers++;
        this.isCorrect = false;
      }

      this.isAnswered = true;
      // 3 másodperc után automatikusan új kérdés
      setTimeout(() => {
        this.setRandomQuestion();
      }, 3000); // 3000 ms = 3 másodperc
    },
    nextQuestion() {
      this.setRandomQuestion();
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
  width: 100%;
  padding-top: 5%;
  box-sizing: border-box;
  text-align: center;
  background-color: #021925;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  color: white;
  overflow: hidden;
}

h1 {
  padding-bottom: 20px;
}


input, button {
  width: 300px;
  margin: 10px auto; /* Középre igazítja a gombokat */
  padding: 8px;
  font-size: 16px;
  border-radius: 30px;
  box-sizing: border-box;
  display: inline-block;
  box-shadow: #000000 4px 4px 0 0;
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
  display: block; /* A gombok mindig egymás alatt lesznek */

}

#nextq {
  background-color: #009cde;
}

#kerdes {
  padding-bottom: 20px;
}

button:hover {
  background-color: #021925;
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
  max-width: 300px;
  height: auto;
  border-radius: 30px;
  box-shadow: #000000 4px 4px 0 0;
  margin-bottom: 50px;

}

.reset-button {
  background-color: #ecaf07;
}
.reset-button:hover {
  background-color: #886400;
  transition: all 175ms cubic-bezier(0, 0, 1, 1);
}

.statistics {
  margin-top: 20px;
  font-size: 16px;
  font-weight: bold;
  display: flex; /* Flexbox használata */
  justify-content: center; /* Középre igazítás */
  gap: 20px; /* Különbség a számok között */
}

.correct-answers,
.incorrect-answers {
  font-size: 54px; /* Nagyobb méret a számoknak */
}
</style>
