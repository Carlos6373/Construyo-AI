git clone https://github.com/tu-usuario/voice-app-builder.git
cd voice-app-builder
# app.py

import streamlit as st
import openai

# 🔐 Sustituye con tu propia clave API de OpenAI
openai.api_key = st.secrets.get("OPENAI_API_KEY", "sk-...")

st.set_page_config(page_title="🛠️ Generador de Apps con IA", layout="wide")
st.title("🛠️ Generador de Apps con IA (Texto → Código)")

st.markdown("""
Escribe en lenguaje natural lo que quieres que tu app haga. Por ejemplo:

`"Quiero una app que muestre un formulario para enviar correos y tenga un diseño bonito"`

👇""")

# Entrada del usuario
user_prompt = st.text_area("✍️ Describe tu app aquí:", height=150)

# Botón para generar código
if st.button("🚀 Generar Código"):
    if not user_prompt.strip():
        st.warning("Por favor, escribe una descripción válida.")
    else:
        with st.spinner("Generando código con IA..."):
            response = openai.ChatCompletion.create(
                model="gpt-4",
                messages=[
                    {
                        "role": "system",
                        "content": (
                            "Eres un desarrollador experto. Genera únicamente el código HTML/CSS/JS "
                            "para una app sencilla basada en la descripción del usuario. No incluyas explicaciones."
                        )
                    },
                    {
                        "role": "user",
                        "content": user_prompt
                    }
                ],
                temperature=0.5,
                max_tokens=800
            )
            code = response['choices'][0]['message']['content']

            st.subheader("💡 Código Generado:")
            st.code(code, language='html')

            st.subheader("🔍 Vista Previa:")
            try:
                st.components.v1.html(code, height=500, scrolling=True)
            except:
                st.error("No se pudo renderizar la vista previa. Verifica que el código generado sea válido.")
