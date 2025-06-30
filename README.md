import gradio as gr
import openai
from google.colab import userdata
import os

# Initialize OpenAI API
# Make sure to add your OpenAI API key to Colab Secrets with the name 'OPENAI_API_KEY'
openai.api_key = userdata.get('OPENAI_API_KEY')

def generate_post_openai(theme, tone, platform):
    """
    Generate a social media caption based on input theme, tone, and platform using OpenAI API.
    """
    if not openai.api_key:
        return "Error: OpenAI API key not found. Please add it to Colab Secrets."

    prompt = (f"Create a {tone} social media post and caption for {platform} about '{theme}'. "
              f"Ensure it aligns with {platform}'s audience style.")

    try:
        response = openai.Completion.create(
            engine="text-davinci-003",  # You can use other engines as well
            prompt=prompt,
            max_tokens=150,
            temperature=0.7
        )

        result = response.choices[0].text.strip()
        return result

    except Exception as e:
        return f"Error: {str(e)}"

# Gradio interface
with gr.Blocks() as app:
    gr.Markdown("# Social Media Post & Caption Generator (using OpenAI)")

    theme = gr.Textbox(label="Theme/Topic(What You Want to Generate )", placeholder="E.g., Travel to Paris, New Tech Gadget")
    tone = gr.Radio(["Casual", "Professional", "Witty", "Inspirational"], label="Tone", value="Casual")
    platform = gr.Dropdown(["Instagram", "Twitter", "LinkedIn", "Facebook"], label="Platform", value="Instagram")

    generate_button = gr.Button("Generate Post")
    output = gr.Textbox(label="Generated Post and Caption")

    generate_button.click(generate_post_openai, [theme, tone, platform], output)

# Launch the app
app.launch(debug=True)
