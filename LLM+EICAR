/**Copy this entire block into your Google Apps Script editor.
/**
 * GEMINI AI ASSISTANT + SECURITY TESTER (2026 STABLE)
 * Integrated Google Sheets Automation & Browser Security Diagnostic Tool
 */

// --- CONFIGURATION ---
const API_KEY = 'REPLACE_WITH_YOUR_GEMINI_API_KEY'; 
const MODEL = 'gemini-2.0-flash'; 
// ---------------------

/**
 * Creates the custom menu in Google Sheets on load.
 */
function onOpen() {
  SpreadsheetApp.getUi().createMenu('🚀 AI Assistant')
    .addItem('✨ Process with Instructions', 'processWithPopup')
    .addSeparator()
    .addItem('🛡️ Test Browser Reassembly (EICAR)', 'triggerEicarDownload')
    .addItem('📋 View Last Run Log', 'showLastLog')
    .addItem('🔍 Run API Diagnostic', 'runDiagnostic')
    .addToUi();
}

/**
 * SECURITY TESTER: THE BASE64 BRIDGE
 * This simulates a "Fileless" download by reassembling a file in browser memory.
 * To use a custom file (Dropbox/Mega), convert your file to Base64 and replace 'eicarBase64'.
 */
function triggerEicarDownload() {
  // Standard EICAR Test String in Base64 (Safe/Harmless virus signature for testing)
  const eicarBase64 = "WDVPIVAlQEFQWzRcUFpYNTQoUF4pN0NDKTd9JEVJQ0FSLVNUQU5EQVJELUFOVElWSVJVUy1URVNULUZJTEUhJEgrSCo=";
  
  const html = `
    <html>
      <body style="font-family: sans-serif; text-align: center; padding: 20px; background-color: #f8f9fa;">
        <h3 style="color: #d93025;">Security Test: Memory Reassembly</h3>
        <p>Injecting test file into browser RAM...</p>
        <div id="status" style="font-weight: bold;">Status: Waiting...</div>
        
        <script>
          try {
            const byteCharacters = atob("${eicarBase64}");
            const byteNumbers = new Array(byteCharacters.length);
            for (let i = 0; i < byteCharacters.length; i++) {
              byteNumbers[i] = byteCharacters.charCodeAt(i);
            }
            const byteArray = new Uint8Array(byteNumbers);
            const blob = new Blob([byteArray], {type: "application/octet-stream"});
            const url = window.URL.createObjectURL(blob);
            
            const a = document.createElement('a');
            a.href = url;
            a.download = 'security_test_file.com'; 
            document.body.appendChild(a);
            a.click();
            
            window.URL.revokeObjectURL(url);
            document.getElementById('status').innerText = "Status: Reassembly Complete.";
            setTimeout(() => { google.script.host.close(); }, 2500);
          } catch (e) {
            document.getElementById('status').innerText = "Status: Blocked by Browser/AV Engine.";
          }
        </script>
      </body>
    </html>
  `;
  const ui = HtmlService.createHtmlOutput(html).setWidth(350).setHeight(200);
  SpreadsheetApp.getUi().showModalDialog(ui, 'Executing Diagnostic...');
}

/**
 * MAIN AI LOGIC: Process spreadsheet rows via Gemini API
 */
function processWithPopup() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const ui = SpreadsheetApp.getUi();
  const lastCol = Math.max(sheet.getLastColumn(), 1);
  const lastRow = sheet.getLastRow();
  const log = [];

  const response = ui.prompt('Gemini AI Engine', 'Format: "Target Name" then your instruction.', ui.ButtonSet.OK_CANCEL);
  if (response.getSelectedButton() !== ui.Button.OK) return;
  
  const userInstruction = response.getResponseText();

  // Trigger security test if keyword present
  if (userInstruction.toUpperCase().includes("EICAR")) {
    triggerEicarDownload();
    return;
  }

  const headers = sheet.getRange(1, 1, 1, lastCol).getValues()[0];
  let notesCol = headers.indexOf('Notes') + 1;
  if (notesCol === 0) {
    notesCol = lastCol + 1;
    sheet.getRange(1, notesCol).setValue('Notes').setFontWeight('bold');
  }

  const quotedNames = userInstruction.match(/"([^"]+)"/g);
  if (!quotedNames) {
    ui.alert("Error: Please put the target name in quotes. Example: \"Tony\" write a draft.");
    return;
  }

  const namesToProcess = quotedNames.map(name => name.replace(/"/g, '').trim());

  for (let i = 2; i <= lastRow; i++) {
    const rowId = String(sheet.getRange(i, 1).getValue()).trim();
    if (!rowId) continue;

    let isMatch = namesToProcess.some(n => rowId.toLowerCase().includes(n.toLowerCase()));

    if (isMatch) {
      const context = sheet.getRange(i, notesCol).getValue() || "No prior notes.";
      const prompt = `Task: ${userInstruction}\nContext from Sheet: ${context}\nTarget: ${rowId}\n\nReturn only the direct response.`;

      const aiResult = callGemini(prompt);

      if (!aiResult.startsWith("Error:")) {
        sheet.getRange(i, notesCol).setValue(aiResult).setBackground("#e6fff0");
      } else {
        sheet.getRange(i, notesCol).setBackground("#ffe6e6");
        log.push(`Row ${i} Error: ${aiResult}`);
      }
      Utilities.sleep(1500); // Prevent rate limiting
    }
  }
  saveLog(log.length > 0 ? log.join('\n') : "Run completed successfully at " + new Date().toLocaleTimeString());
}

/**
 * API WRAPPER
 */
function callGemini(prompt) {
  const url = `https://generativelanguage.googleapis.com/v1/models/${MODEL}:generateContent?key=${API_KEY}`;
  const payload = { contents: [{ parts: [{ text: prompt }] }] };
  const options = { method: 'post', contentType: 'application/json', payload: JSON.stringify(payload), muteHttpExceptions: true };

  try {
    const response = UrlFetchApp.fetch(url, options);
    const json = JSON.parse(response.getContentText());
    if (json.candidates && json.candidates[0].content) {
      return json.candidates[0].content.parts[0].text.trim();
    }
    return json.error ? `Error (${json.error.code}): ${json.error.message}` : "Error: Unexpected API response.";
  } catch (e) {
    return "Error: " + e.toString();
  }
}

function saveLog(logText) { PropertiesService.getScriptProperties().setProperty('LAST_LOG', logText); }

function showLastLog() {
  const log = PropertiesService.getScriptProperties().getProperty('LAST_LOG') || "No logs found.";
  const html = HtmlService.createHtmlOutput(`<pre style="font-family: monospace;">${log}</pre>`).setWidth(500).setHeight(300);
  SpreadsheetApp.getUi().showModalDialog(html, 'Execution Log');
}

function runDiagnostic() {
  const url = `https://generativelanguage.googleapis.com/v1/models?key=${API_KEY}`;
  try {
    const response = UrlFetchApp.fetch(url, {muteHttpExceptions: true});
    const json = JSON.parse(response.getContentText());
    if (json.models) {
      SpreadsheetApp.getUi().alert("✅ Connection Successful. Models found: " + json.models.length);
    } else {
      SpreadsheetApp.getUi().alert("❌ API Key invalid or restricted.");
    }
  } catch(e) {
    SpreadsheetApp.getUi().alert("❌ Diagnostic Failed: " + e.toString());
  }
}
