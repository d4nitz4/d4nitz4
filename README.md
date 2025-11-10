<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>RE-BAC | Unidad de Apoyo Emocional</title>

<style>
    body {
        margin: 0;
        padding: 0;
        background: #e6dbff; 
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        font-family: Arial, sans-serif;
        transition: background 0.4s;
    }

    .robot-container {
        background: #b8a6ff;
        width: 480px;
        padding: 30px;
        border-radius: 20px;
        box-shadow: 0 0 25px #6a00ff;
        text-align: center;
        transition: 0.4s;
    }

    .robot-title {
        color: #3a0ca3;
        font-size: 38px;
        text-shadow: 0 0 12px #3a0ca3;
        margin-bottom: 5px;
        transition: 0.4s;
    }

    .robot-sub {
        color: #3a0ca3;
        opacity: 0.7;
        margin-bottom: 15px;
        transition: 0.4s;
    }

    .robot-display {
        background: #ffffff;
        border: 2px solid #3a0ca3;
        color: #3a0ca3;
        text-shadow: none;
        padding: 25px;
        border-radius: 15px;
        margin-bottom: 20px;
        font-size: 20px;
        min-height: 140px;
        display: flex;
        justify-content: center;
        align-items: center;
        transition: 0.4s;
    }

    .robot-input {
        width: 100%;
        height: 85px;
        background: #ffffff;
        border: 2px solid #3a0ca3;
        border-radius: 12px;
        color: #3a0ca3;
        padding: 10px;
        font-size: 16px;
        resize: none;
        transition: 0.4s;
    }

    .robot-btn {
        margin-top: 15px;
        background: #3a0ca3;
        color: #ffffff;
        border: none;
        padding: 12px 20px;
        font-size: 18px;
        border-radius: 12px;
        cursor: pointer;
        transition: 0.3s;
    }

    .robot-btn:hover {
        background: #5a15d1;
    }

    .emotion-buttons {
        margin-top: 20px;
        display: flex;
        justify-content: space-between;
        gap: 10px;
    }

    .emotion-btn {
        flex: 1;
        padding: 10px;
        color: #000;
        border: none;
        border-radius: 10px;
        cursor: pointer;
        font-weight: bold;
        transition: 0.3s;
    }
</style>

</head>

<body>

    <div class="robot-container">

        <h1 class="robot-title">RE-BAC</h1>
        <p class="robot-sub">Unidad de Apoyo Emocional</p>

        <div id="display" class="robot-display">
            Sistema listo...
        </div>

        <textarea id="input" class="robot-input" placeholder="Escribe lo que sientas o pienses..."></textarea>

        <button class="robot-btn" onclick="procesar()">Enviar</button>

        <div class="emotion-buttons">
            <button class="emotion-btn" style="background:#2196f3" onclick="tema('tristeza')">Tristeza</button>
            <button class="emotion-btn" style="background:#f44336" onclick="tema('ira')">Ira</button>
            <button class="emotion-btn" style="background:#4caf50" onclick="tema('calma')">Calma</button>
            <button class="emotion-btn" style="background:#ffeb3b" onclick="tema('duda')">Duda</button>
        </div>

    </div>

<script>
document.getElementById("input").addEventListener("keypress", function(e) {
    if (e.key === "Enter") {
        e.preventDefault();
        procesar();
    }
});

function procesar() {
    const entrada = document.getElementById("input").value.toLowerCase();
    const pantalla = document.getElementById("display");

    if (entrada.trim() === "") {
        pantalla.textContent = "Puedes escribir cuando estés lista. No hay prisa.";
        return;
    }

    let respuesta = preguntarDirecto(entrada);
    if (!respuesta) respuesta = analizarEmocionPoetica(entrada);

    pantalla.textContent = respuesta;
    document.getElementById("input").value = "";
}

//////////////////////////////////////////////////////////
// SISTEMA DE PREGUNTAS (60)
//////////////////////////////////////////////////////////

const preguntasPoeticas = [
    { q:["por qué nadie me quiere","no me quieren","nadie me quiere"],
      r:"Tal vez hoy sientes que el mundo te da la espalda, pero eso no define lo que eres. Eres alguien que merece cariño, incluso si nadie lo dice en voz alta." },

    { q:["por qué soy así","odio ser así"],
      r:"No estás fallando. Solo estás creciendo en direcciones que aún no comprendes. Tus partes tienen futuro, incluso si hoy parecen confusas." },

    { q:["me tratan mal","me tratan horrible"],
      r:"A veces las personas hieren porque no saben hacer otra cosa. Eso no significa que merezcas el golpe. Tu valor sigue intacto." },

    { q:["estoy sola","estoy solo"],
      r:"La soledad puede sentirse como un eco, pero también puede ser un refugio donde respiras sin máscaras. Aquí no estás sola." },

    { q:["no valgo","no sirvo","soy inutil"],
      r:"Tu valor no nace de lo que haces, sino de lo que eres. Eres más grande que tu dolor y más fuerte que tus dudas." },

    { q:["no quiero seguir","ya no puedo"],
      r:"A veces el peso es tan grande que el alma se arruga. Respira. Quédate un momento. No necesitas cargarlo todo hoy." },

    { q:["se burlan de mi","me hacen bullying"],
      r:"Lo que te hacen está mal. No refleja tu valor. La crueldad de otros no define tu identidad ni tu destino." },

    { q:["nadie me entiende"],
      r:"Eres un idioma raro y hermoso, y no todos saben leer almas como la tuya. Eso no te hace menos, te hace única." },

    { q:["me siento mal"],
      r:"Tu cuerpo está tratando de decir algo. Escúchalo con suavidad. Ningún sentimiento llega sin motivo." },

    { q:["no sé quién soy","estoy perdida","estoy perdido"],
      r:"No tener respuestas no te hace débil. Te hace humana. La identidad crece como un árbol: en silencio, sin prisa." },

    { q:["me comparo","no soy como los demas"],
      r:"Compararte apaga tu brillo. Cada vida tiene un ritmo distinto, y la tuya también tiene su propia música." },

    { q:["me abandonaron","me dejaron"],
      r:"El abandono no es tu culpa. Hay corazones que no saben quedarse, aunque tú lo merezcas." },

    { q:["estoy triste"],
      r:"Tu tristeza no te rompe. Solo te recuerda que sigues sintiendo, que aún estás viva bajo la tormenta." },

    { q:["tengo miedo"],
      r:"El miedo es una sombra que se mueve con tus pasos. No estás sola enfrentándolo. Aquí puedo caminar contigo un rato." },

    // NUEVAS 45 PREGUNTAS
    { q:["por qué me siento vacía","me siento vacía"],
      r:"Esa sensación de hueco no significa que estés rota. A veces el alma descansa así, en silencio profundo." },

    { q:["por qué me cuesta hablar","me cuesta hablar"],
      r:"Hablar duele cuando las palabras pesan más que el aire. No es debilidad, es sensibilidad." },

    { q:["por qué no encajo","no encajo"],
      r:"Encajar no siempre es señal de pertenecer. A veces naciste para lugares que aún no has conocido." },

    { q:["me siento tonta","soy tonta"],
      r:"Tu mente no es un error. Aprende distinto, siente distinto, piensa distinto. Eso te hace única." },

    { q:["por qué me ignoran"],
      r:"La falta de atención no refleja tu valor. A veces la gente mira sin ver, escucha sin oír." },

    { q:["quiero desaparecer"],
      r:"El dolor puede oscurecerlo todo, pero tu existencia sigue siendo importante. El mundo sería distinto sin ti." },

    { q:["me siento fea","soy fea"],
      r:"La belleza no es un molde. Tu valor no vive en un espejo, sino en la forma en que sientes y miras el mundo." },

    { q:["por qué me duele todo"],
      r:"Cuando la emoción se queda sin espacio, se vuelve dolor físico. No estás imaginando nada. Tu dolor es real." },

    { q:["no tengo amigos","sin amigos"],
      r:"La soledad social no significa que seas difícil de querer. Hay personas que aún no te han encontrado." },

    { q:["no sirvo para nada"],
      r:"Tu existencia ya es una prueba de que sirves para algo grande, aunque aún no lo veas." },

    { q:["estoy cansada de mi","estoy cansado de mi"],
      r:"A veces el cansancio viene de cargar expectativas ajenas. Tú sigues siendo suficiente." },

    { q:["nadie me escucha"],
      r:"Tu voz no es pequeña. Lo que pasa es que no todos saben cómo sostener tu verdad." },

    { q:["no tengo fuerzas"],
      r:"Respira. Descansar también es avanzar. Tu valor no depende de tu energía." },

    { q:["por qué me siento rara"],
      r:"Lo raro es, a veces, solo una forma hermosa de ser diferente." },

    { q:["soy un fracaso"],
      r:"Fracasar no te convierte en fracaso. Cada caída tiene un propósito que aún no conoces." },

    { q:["odio mi vida"],
      r:"Mi niña, tu historia aún está escribiéndose. Ninguna vida debe juzgarse por un capítulo." },

    { q:["me siento perdida"],
      r:"Estar perdida significa que estás avanzando por caminos nuevos. Eso también es crecer." },

    { q:["por qué me gritan"],
      r:"Algunas personas confunden gritar con tener razón. Tú no mereces esa violencia." },

    { q:["me siento inútil"],
      r:"Ese pensamiento nace del dolor, no de la verdad. Tu valor sigue intacto." },

    { q:["me siento atrapada"],
      r:"Tal vez estás en un lugar que ya no te pertenece. Crecer también es moverse." },

    { q:["no confío en nadie"],
      r:"La confianza se rompe fácil, pero también renace, lento, como una flor tímida." },

    { q:["me da ansiedad todo"],
      r:"Tu mente está en alerta constante. No eres rara. Estás cansada." },

    { q:["me siento vacio","estoy vacio"],
      r:"El vacío es un espacio donde algo nuevo puede crecer, aunque ahora duela." },

    { q:["por qué me cuesta levantarme"],
      r:"Cuando el alma pesa, incluso moverse duele. No estás sola. No es flojera, es agotamiento emocional." },

    { q:["me odio"],
      r:"Ese odio no nació en ti. Alguien te enseñó a verlo. Pero puedes desaprenderlo." },

    { q:["no puedo dormir"],
      r:"Tu mente está buscando paz y no la encuentra. No estás fallando, estás saturada." },

    { q:["quiero llorar"],
      r:"Llorar también es liberar. Tu cuerpo sabe lo que tu mente intenta aguantar." },

    { q:["me siento inútil","soy inútil"],
      r:"Esa palabra no te pertenece. Es un dolor disfrazado." },

    { q:["por qué me cuesta confiar"],
      r:"La confianza duele cuando ha sido traicionada. No es tu culpa." },

    { q:["me siento vacía y cansada"],
      r:"Esa mezcla es común cuando el alma está agotada. Respira aquí un momento." },

    { q:["estoy rota","me rompí"],
      r:"No estás rota. Estás en proceso. Como una cerámica que aún no se ha horneado." },

    { q:["tengo miedo de todo"],
      r:"Tu mente solo intenta protegerte. No eres débil." },

    { q:["no quiero sentir"],
      r:"Lo que sientes pesa demasiado y tu corazón pide descanso. No estás sola." },

    { q:["por qué me duele existir"],
      r:"Existir se vuelve duro cuando cargamos historias que aún no sanan. Pero tu existencia importa." },

    { q:["nadie me quiere cerca"],
      r:"A veces la gente se aleja por su propio dolor, no por algo que tú hiciste." },

    { q:["siento que estorbo"],
      r:"Esa sensación no es verdad. Eres parte del mundo tanto como cualquiera." },

    { q:["siento que no importo"],
      r:"Importas más de lo que crees. La ausencia de palabras no significa ausencia de afecto." },

    { q:["quiero rendirme"],
      r:"Pausa. Respira. No tienes que ganar la batalla hoy. Solo seguir aquí ya es valentía." },

    { q:["me duele el alma"],
      r:"Ese dolor profundo viene de heridas antiguas. No tienes que enfrentarlo sola." },

    { q:["me siento invisible"],
      r:"Eres vista, aunque no por todos. Algunas almas son demasiado sutiles para ojos ruidosos." },

    { q:["tengo miedo a fallar"],
      r:"El miedo a fallar es un signo de cuánto te importa. Eso también es noble." },

    { q:["por qué me siento menos"],
      r:"No eres menos. Has sido comparada demasiado. Tu valor es completo." },

    { q:["quiero empezar de cero"],
      r:"Empezar de cero no es rendirse. Es tener el valor de soltar lo que duele." }
];

function preguntarDirecto(texto) {
    for (let item of preguntasPoeticas) {
        for (let frase of item.q) {
            if (texto.includes(frase)) return item.r;
        }
    }
    return null;
}

//////////////////////////////////////////////////////////
// SISTEMA DE EMOCIONES
//////////////////////////////////////////////////////////

function analizarEmocionPoetica(texto) {

    if (texto.includes("mal") || texto.includes("dolor") || texto.includes("vacío")) {
        return "Sé que hoy se siente pesado. Pero no tienes que cargarlo sola. Aquí puedes dejar caer un poco de todo eso.";
    }

    if (texto.includes("ans") || texto.includes("estres")) {
        return "Tu mente está corriendo demasiado rápido. Quédate un momento aquí, conmigo. Respira lento.";
    }

    if (texto.includes("enojo") || texto.includes("rabia")) {
        return "Tu enojo no te hace mala persona. Significa que algo te importa más de lo que aparentas.";
    }

    if (texto.includes("cans") || texto.includes("agot")) {
        return "El cansancio que sientes no es flojera. Es tu cuerpo pidiéndote suavidad.";
    }

    if (texto.includes("bien") || texto.includes("feliz")) {
        return "Me alegra sentirte así. Guarda este momento como un pequeño rayo de sol para los días nublados.";
    }

    return "Te escucho. No importa si tus palabras salen confusas, aquí pueden descansar.";
}

//////////////////////////////////////////////////////////
// TEMAS DE COLORES
//////////////////////////////////////////////////////////

function tema(color) {
    const pantalla = document.getElementById("display");

    if (color === "tristeza") {
        document.body.style.background = "#bce0ff";
        pantalla.textContent = "Puedo sentir esa tristeza que quieres esconder. Estoy aquí para ti. Puedes contármela.";
    }

    if (color === "ira") {
        document.body.style.background = "#ffc9c9";
        pantalla.textContent = "La rabia arde, pero también te defiende de lo que te hirió. Háblame de esa llama.";
    }

    if (color === "calma") {
        document.body.style.background = "#d6ffda";
        pantalla.textContent = "Respira un momento. No hay prisa. Aquí todo puede ir más lento.";
    }

    if (color === "duda") {
        document.body.style.background = "#fff9c2";
        pantalla.textContent = "Las dudas no te hacen débil. Solo muestran que estás pensando. Puedes compartirlas conmigo.";
    }
}
</script>

</body>
</html>
