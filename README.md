import streamlit as st
import google.generativeai as genai

# --- CONFIGURACIÓN DE LA IA ---
# Para mayor seguridad, usaremos las "Secrets" de Streamlit Cloud
try:
    api_key = st.secrets["GOOGLE_API_KEY"]
    genai.configure(api_key=api_key)
    model = genai.GenerativeModel('gemini-1.5-flash')
except:
    st.warning("⚠️ Falta la API Key. Configúrala en los 'Secrets' de Streamlit para que la IA funcione.")

# --- FUNCIONES LÓGICAS ---
def analizar_escritura(texto):
    prompt = f"""
    Eres un experto en Alfabetización Académica universitaria. 
    Analiza este texto de un estudiante (18-25 años) y detecta:
    1. Errores de registro (¿es demasiado informal?).
    2. Problemas de cohesión (¿faltan conectores?).
    3. Ausencia de citas o voces de autoridad.
    Entrega una respuesta con: 'Fortalezas', 'Áreas a mejorar' y un 'Consejo práctico'.
    Texto: {texto}
    """
    response = model.generate_content(prompt)
    return response.text

def traducir_a_academico(frase):
    prompt = f"Actúa como un traductor de registro. Convierte esta frase informal en una formal de nivel universitario: '{frase}'"
    response = model.generate_content(prompt)
    return response.text

# --- INTERFAZ DE USUARIO (UI) ---
st.set_page_config(page_title="Littera AI", page_icon="🎓")

st.title("🎓 Littera: Alfabetización Académica")
st.markdown("---")

menu = ["🏠 Inicio", "🔍 Diagnóstico IA", "✍️ Traductor Académico", "📊 Panel Docente"]
choice = st.sidebar.selectbox("Navegación", menu)

if choice == "🏠 Inicio":
    st.subheader("Bienvenido a la herramienta de apoyo universitario")
    st.write("Littera ayuda a estudiantes a cerrar la brecha entre la secundaria y la universidad.")
    st.info("Selecciona una herramienta en el menú de la izquierda para comenzar.")

elif choice == "🔍 Diagnóstico IA":
    st.header("Análisis Inteligente")
    texto_usuario = st.text_area("Pega tu párrafo o ensayo aquí:", height=300)
    if st.button("Analizar Texto"):
        with st.spinner("La IA está evaluando tu escritura..."):
            resultado = analizar_escritura(texto_usuario)
            st.markdown(resultado)

elif choice == "✍️ Traductor Académico":
    st.header("Traductor de Registro")
    st.write("Escribe una idea de forma simple y la IA la elevará a un nivel académico.")
    idea = st.text_input("Tu idea:", placeholder="Ej: El autor dice cosas que no se entienden...")
    if st.button("Refinar Lenguaje"):
        with st.spinner("Transformando..."):
            formal = traducir_a_academico(idea)
            st.success(f"**Versión sugerida:** {formal}")

elif choice == "📊 Panel Docente":
    st.header("Estadísticas del Aula")
    st.write("Resumen de las dificultades más frecuentes detectadas en los estudiantes.")
    # Datos simulados para el docente
    dificultades = {"Registro Informal": 45, "Citas APA": 60, "Cohesión": 30}
    st.bar_chart(dificultades)
    st.write("💡 **Sugerencia:** El 60% de los alumnos tiene dudas con las citas. Agenda un taller de APA.")
