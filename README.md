import pytubefix
import ffmpeg
import openai

openai.api_key = "Cole o link aqui: "  
client = openai

# Baixa o audio do arquivo
import sys 
url = sys.argv[1]
filename = "audio.wav"
yt = pytubefix.YouTube(url)
stream = yt.streams[0].url
ffmpeg.input(stream).output(filename,
                            format='wav' ,
                            loglevel="error")

# Cria a transcrição
audio_file = open(filename, "rb")
transcript = client.audio.transcripts.create(
    model="whisper-1",
    file=audio_file
).text

# Pede pela revisão
completion = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system",
        "content": """
            Você é um assistente que resume videos detalhados.
            Responda com formatação Markdown.
        """},

        {"role": "user",
         "content": f"Descreva o seguinte video: {transcript}"}
    ])

with open(f"resumo.md", "w+") as md:
    md.write(completion.choices[0].message.content)# AnalisadorDeVideos
