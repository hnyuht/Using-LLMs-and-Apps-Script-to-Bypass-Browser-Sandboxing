# Using-LLMs-and-Apps-Script-to-Bypass-Browser-Sandboxing

Gemini AI Sheets Engine & Security Diagnostic
A Google Apps Script integration that connects Google Sheets to Gemini 2.0 Flash. This tool automates spreadsheet workflows via natural language and includes a "Browser Reassembly" module for security testing.

✨ Features
Natural Language Processing: Use quotes to target specific rows (e.g., "Company X" summarize the notes).

Context Awareness: Automatically reads data from your 'Notes' column to inform AI responses.

Security Diagnostic: Built-in Base64 file reassembly tool to test how Endpoint Detection (EDR) and Browser Engines handle memory-injected files.

Error Logging: Visual feedback (Green/Red cell colors) and a dedicated log viewer.

🛠️ Installation
Get an API Key: Obtain one for free at Google AI Studio.

Open Apps Script: In Google Sheets, go to Extensions > Apps Script.

Paste Code: Replace the default code with Code.gs from this repo.

Configure: Insert your API key in the API_KEY variable at the top of the script.

Authorize: Run the onOpen function once to grant necessary permissions.

🛡️ Security Testing Note
The triggerEicarDownload function uses a standard, harmless EICAR string. To test your own files (e.g., from Dropbox):

Convert your file to Base64.

Replace the eicarBase64 variable in the script.

This bypasses standard URL fetching to test if your security software monitors browser memory.

📜 Disclaimer
This project is for educational use. Users are responsible for ensuring their use of the Gemini API complies with local laws and Google's Terms of Service.
