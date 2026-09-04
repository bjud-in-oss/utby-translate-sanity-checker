# Steg 2a: Förändra Utåt - Vision (TCK-003)

### Målbild för användarupplevelsen
1. **Omedelbar ljudåterkoppling (Streaming Audio):**
   - Användaren skickar sin inspelade testsekvens.
   - Så fort Gemini Live Translate genererar sina första ord börjar ljudet omedelbart strömma ut ur högtalarna i realtid.
   - Användaren slipper vänta på att Gemini pratat färdigt för att höra översättningen.
2. **Tydlig och transparent status:**
   - Direkt när första ljudchunken anländer växlar statusindikatorn till en pulserande grön etikett: `"Översätter & Spelar upp i realtid..."`.
3. **Full kontroll med manuell avbrottsknapp:**
   - En distinkt röd/mörkgrå "Stäng anslutning / Stoppa"-knapp finns omedelbart tillgänglig.
   - Klick på knappen bryter WebSocket-anslutningen och klipper omedelbart ljudet så att användaren kan återta kontrollen direkt.
