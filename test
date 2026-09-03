# Nejdříve je potřeba nainstalovat knihovny:
# pip install streamlit youtube-transcript-api

import streamlit as st
from youtube_transcript_api import YouTubeTranscriptApi
from urllib.parse import urlparse, parse_qs

st.title("📹 YouTube Video do Markdown Transkriptu")
url = st.text_input("Vložte odkaz na YouTube video:")

if st.button("Vygenerovat Markdown"):
    if url:
        try:
            # 1. Získání Video ID z URL
            parsed_url = urlparse(url)
            video_id = parse_qs(parsed_url.query).get('v')
            if not video_id:
                # Pro zkrácené youtu.be odkazy
                video_id = [parsed_url.path.strip('/')]
            
            video_id = video_id[0]

            # 2. Stažení transkriptu (zkusí češtinu, pak angličtinu)
            transcript = YouTubeTranscriptApi.get_transcript(video_id, languages=['cs', 'en'])
            
            # 3. Formátování do Markdownu
            md_content = f"# Transkript videa\n**Zdroj:** {url}\n\n## Obsah:\n\n"
            for line in transcript:
                # Převede sekundy na formát MM:SS
                minutes = int(line['start'] // 60)
                seconds = int(line['start'] % 60)
                time_str = f"[{minutes:02d}:{seconds:02d}]"
                
                md_content += f"* **{time_str}** {line['text']}\n"
            
            st.success("Transkript byl úspěšně vygenerován!")
            st.markdown("### Náhled:")
            st.text_area("", value=md_content, height=300)

            # 4. Tlačítko pro stažení .md souboru
            st.download_button(
                label="📥 Stáhnout jako .md",
                data=md_content,
                file_name=f"transkript_{video_id}.md",
                mime="text/markdown"
            )

        except Exception as e:
            st.error(f"Nepodařilo se získat transkript. (Chyba: {e})")
    else:
        st.warning("Prosím, vložte URL.")
