<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SOAP Note Scanner & Insurance Form Filler</title>
    <script src='https://unpkg.com/tesseract.js@4.0.2/dist/tesseract.min.js'></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
            animation: fadeInDown 0.8s ease-out;
        }

        .header p {
            font-size: 1.1em;
            opacity: 0.9;
            animation: fadeInUp 0.8s ease-out;
        }

        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            padding: 30px;
        }

        .section {
            background: white;
            border-radius: 15px;
            padding: 25px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }

        .section:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
        }

        .section h2 {
            color: #667eea;
            margin-bottom: 20px;
            font-size: 1.5em;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .upload-area {
            border: 3px dashed #667eea;
            border-radius: 10px;
            padding: 40px;
            text-align: center;
            background: #f8f9ff;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .upload-area:hover {
            background: #e8ebff;
            border-color: #764ba2;
        }

        .upload-area.dragover {
            background: #dce0ff;
            transform: scale(1.02);
        }

        #fileInput {
            display: none;
        }

        .upload-icon {
            font-size: 48px;
            color: #667eea;
            margin-bottom: 10px;
        }

        #imagePreview {
            max-width: 100%;
            max-height: 400px;
            margin: 20px auto;
            display: none;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }

        .button {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 30px;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            transition: all 0.3s ease;
            margin: 10px 5px;
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3);
        }

        .button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4);
        }

        .button:disabled {
            background: #ccc;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        .progress-bar {
            width: 100%;
            height: 20px;
            background: #f0f0f0;
            border-radius: 10px;
            overflow: hidden;
            margin: 20px 0;
            display: none;
        }

        .progress-fill {
            height: 100%;
            background: linear-gradient(90deg, #667eea 0%, #764ba2 100%);
            width: 0%;
            transition: width 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .progress-fill::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            bottom: 0;
            right: 0;
            background: linear-gradient(
                90deg,
                transparent,
                rgba(255, 255, 255, 0.3),
                transparent
            );
            animation: shimmer 1.5s infinite;
        }

        @keyframes shimmer {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        .extracted-text {
            background: #f8f9ff;
            border-radius: 10px;
            padding: 20px;
            margin-top: 20px;
            max-height: 300px;
            overflow-y: auto;
            font-family: 'Courier New', monospace;
            white-space: pre-wrap;
            line-height: 1.6;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: 600;
            color: #333;
        }

        .form-group input,
        .form-group textarea {
            width: 100%;
            padding: 10px 15px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 14px;
            transition: border-color 0.3s ease;
        }

        .form-group input:focus,
        .form-group textarea:focus {
            outline: none;
            border-color: #667eea;
        }

        .form-group textarea {
            resize: vertical;
            min-height: 100px;
        }

        .status-message {
            padding: 15px;
            border-radius: 8px;
            margin: 15px 0;
            display: none;
            animation: slideIn 0.5s ease-out;
        }

        .status-message.success {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .status-message.error {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .status-message.info {
            background: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }

        @keyframes fadeInDown {
            from {
                opacity: 0;
                transform: translateY(-20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes fadeInUp {
            from {
                opacity: 0;
                transform: translateY(20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateX(-20px);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        @media (max-width: 768px) {
            .main-content {
                grid-template-columns: 1fr;
            }
            
            .header h1 {
                font-size: 2em;
            }
        }

        .loading-spinner {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid rgba(255, 255, 255, 0.3);
            border-radius: 50%;
            border-top-color: white;
            animation: spin 1s ease-in-out infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🏥 SOAP Note Scanner</h1>
            <p>Convert handwritten medical notes to insurance forms instantly</p>
        </div>

        <div class="main-content">
            <div class="section">
                <h2>📄 Upload SOAP Note</h2>
                <div class="upload-area" id="uploadArea">
                    <div class="upload-icon">📤</div>
                    <p><strong>Click to upload or drag & drop</strong></p>
                    <p style="margin-top: 10px; color: #666; font-size: 14px;">Supports JPG, PNG, PDF</p>
                    <input type="file" id="fileInput" accept="image/*,.pdf">
                </div>
                <img id="imagePreview" alt="Preview">
                
                <div class="progress-bar" id="progressBar">
                    <div class="progress-fill" id="progressFill"></div>
                </div>

                <div style="text-align: center; margin-top: 20px;">
                    <button class="button" id="processButton" disabled>
                        <span id="processButtonText">Process SOAP Note</span>
                        <span class="loading-spinner" id="loadingSpinner" style="display: none;"></span>
                    </button>
                </div>

                <div class="status-message" id="statusMessage"></div>

                <div class="extracted-text" id="extractedText" style="display: none;">
                    <h3 style="margin-bottom: 10px; color: #667eea;">Extracted Text:</h3>
                    <div id="ocrResult"></div>
                </div>
            </div>

            <div class="section">
                <h2>📋 Insurance Form</h2>
                <form id="insuranceForm">
                    <div class="form-group">
                        <label>Patient Name</label>
                        <input type="text" id="patientName" placeholder="Auto-filled from SOAP note">
                    </div>
                    
                    <div class="form-group">
                        <label>Date of Service</label>
                        <input type="date" id="dateOfService">
                    </div>
                    
                    <div class="form-group">
                        <label>Chief Complaint (Subjective)</label>
                        <textarea id="chiefComplaint" placeholder="Patient's reported symptoms"></textarea>
                    </div>
                    
                    <div class="form-group">
                        <label>Clinical Findings (Objective)</label>
                        <textarea id="clinicalFindings" placeholder="Examination results, vital signs"></textarea>
                    </div>
                    
                    <div class="form-group">
                        <label>Diagnosis (Assessment)</label>
                        <input type="text" id="diagnosis" placeholder="ICD-10 code or diagnosis">
                    </div>
                    
                    <div class="form-group">
                        <label>Treatment Plan</label>
                        <textarea id="treatmentPlan" placeholder="Prescribed medications, procedures"></textarea>
                    </div>
                    
                    <div class="form-group">
                        <label>Provider Name</label>
                        <input type="text" id="providerName" placeholder="Dr. Name">
                    </div>
                    
                    <div class="form-group">
                        <label>Provider NPI</label>
                        <input type="text" id="providerNPI" placeholder="10-digit NPI number">
                    </div>

                    <div style="text-align: center; margin-top: 30px;">
                        <button type="button" class="button" onclick="exportForm()">Export Form</button>
                        <button type="button" class="button" onclick="clearForm()">Clear Form</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

    <script>
        let selectedFile = null;
        let ocrWorker = null;

        // Initialize Tesseract worker
        async function initializeOCR() {
            ocrWorker = await Tesseract.createWorker({
                logger: m => {
                    if (m.status === 'recognizing text') {
                        updateProgress(m.progress * 100);
                    }
                }
            });
            await ocrWorker.loadLanguage('eng');
            await ocrWorker.initialize('eng');
        }

        initializeOCR();

        // File upload handling
        const uploadArea = document.getElementById('uploadArea');
        const fileInput = document.getElementById('fileInput');
        const imagePreview = document.getElementById('imagePreview');
        const processButton = document.getElementById('processButton');

        uploadArea.addEventListener('click', () => fileInput.click());

        uploadArea.addEventListener('dragover', (e) => {
            e.preventDefault();
            uploadArea.classList.add('dragover');
        });

        uploadArea.addEventListener('dragleave', () => {
            uploadArea.classList.remove('dragover');
        });

        uploadArea.addEventListener('drop', (e) => {
            e.preventDefault();
            uploadArea.classList.remove('dragover');
            const files = e.dataTransfer.files;
            if (files.length > 0) {
                handleFile(files[0]);
            }
        });

        fileInput.addEventListener('change', (e) => {
            if (e.target.files.length > 0) {
                handleFile(e.target.files[0]);
            }
        });

        function handleFile(file) {
            if (file.type.startsWith('image/')) {
                selectedFile = file;
                const reader = new FileReader();
                reader.onload = (e) => {
                    imagePreview.src = e.target.result;
                    imagePreview.style.display = 'block';
                    processButton.disabled = false;
                    showStatus('Image loaded successfully!', 'success');
                };
                reader.readAsDataURL(file);
            } else {
                showStatus('Please upload an image file (JPG, PNG)', 'error');
            }
        }

        processButton.addEventListener('click', processSOAPNote);

        async function processSOAPNote() {
            if (!selectedFile || !ocrWorker) return;

            const processButtonText = document.getElementById('processButtonText');
            const loadingSpinner = document.getElementById('loadingSpinner');
            
            processButton.disabled = true;
            processButtonText.textContent = 'Processing...';
            loadingSpinner.style.display = 'inline-block';
            
            showProgress();
            showStatus('Processing SOAP note...', 'info');

            try {
                const result = await ocrWorker.recognize(selectedFile);
                const extractedText = result.data.text;
                
                displayExtractedText(extractedText);
                parseAndFillForm(extractedText);
                
                showStatus('SOAP note processed successfully!', 'success');
            } catch (error) {
                showStatus('Error processing image: ' + error.message, 'error');
            } finally {
                processButton.disabled = false;
                processButtonText.textContent = 'Process SOAP Note';
                loadingSpinner.style.display = 'none';
                hideProgress();
            }
        }

        function displayExtractedText(text) {
            const extractedTextDiv = document.getElementById('extractedText');
            const ocrResult = document.getElementById('ocrResult');
            
            extractedTextDiv.style.display = 'block';
            ocrResult.textContent = text;
        }

        function parseAndFillForm(text) {
            // Parse SOAP note structure
            const lines = text.split('\n').map(line => line.trim()).filter(line => line);
            
            // Try to identify SOAP sections
            let currentSection = '';
            const sections = {
                subjective: [],
                objective: [],
                assessment: [],
                plan: []
            };

            lines.forEach(line => {
                const lowerLine = line.toLowerCase();
                
                if (lowerLine.includes('subjective') || lowerLine.startsWith('s:')) {
                    currentSection = 'subjective';
                } else if (lowerLine.includes('objective') || lowerLine.startsWith('o:')) {
                    currentSection = 'objective';
                } else if (lowerLine.includes('assessment') || lowerLine.startsWith('a:')) {
                    currentSection = 'assessment';
                } else if (lowerLine.includes('plan') || lowerLine.startsWith('p:')) {
                    currentSection = 'plan';
                } else if (currentSection) {
                    sections[currentSection].push(line);
                }
            });

            // Extract patient name (usually at the top)
            const namePattern = /(?:patient|name|pt)[:\s]+([A-Za-z\s]+)/i;
            const nameMatch = text.match(namePattern);
            if (nameMatch) {
                document.getElementById('patientName').value = nameMatch[1].trim();
            }

            // Extract date
            const datePattern = /\d{1,2}[\/\-]\d{1,2}[\/\-]\d{2,4}/;
            const dateMatch = text.match(datePattern);
            if (dateMatch) {
                const dateParts = dateMatch[0].split(/[\/\-]/);
                if (dateParts.length === 3) {
                    const year = dateParts[2].length === 2 ? '20' + dateParts[2] : dateParts[2];
                    const formattedDate = `${year}-${dateParts[0].padStart(2, '0')}-${dateParts[1].padStart(2, '0')}`;
                    document.getElementById('dateOfService').value = formattedDate;
                }
            }

            // Fill form sections
            if (sections.subjective.length > 0) {
                document.getElementById('chiefComplaint').value = sections.subjective.join('\n');
            }
            
            if (sections.objective.length > 0) {
                document.getElementById('clinicalFindings').value = sections.objective.join('\n');
            }
            
            if (sections.assessment.length > 0) {
                document.getElementById('diagnosis').value = sections.assessment.join('\n');
            }
            
            if (sections.plan.length > 0) {
                document.getElementById('treatmentPlan').value = sections.plan.join('\n');
            }

            // Extract provider name
            const providerPattern = /(?:dr\.?|doctor|provider)[:\s]+([A-Za-z\s]+)/i;
            const providerMatch = text.match(providerPattern);
            if (providerMatch) {
                document.getElementById('providerName').value = providerMatch[1].trim();
            }
        }

        function showStatus(message, type) {
            const statusMessage = document.getElementById('statusMessage');
            statusMessage.textContent = message;
            statusMessage.className = `status-message ${type}`;
            statusMessage.style.display = 'block';
            
            if (type !== 'info') {
                setTimeout(() => {
                    statusMessage.style.display = 'none';
                }, 5000);
            }
        }

        function showProgress() {
            document.getElementById('progressBar').style.display = 'block';
        }

        function hideProgress() {
            document.getElementById('progressBar').style.display = 'none';
            document.getElementById('progressFill').style.width = '0%';
        }

        function updateProgress(percent) {
            document.getElementById('progressFill').style.width = `${percent}%`;
        }

        function exportForm() {
            const formData = {
                patientName: document.getElementById('patientName').value,
                dateOfService: document.getElementById('dateOfService').value,
                chiefComplaint: document.getElementById('chiefComplaint').value,
                clinicalFindings: document.getElementById('clinicalFindings').value,
                diagnosis: document.getElementById('diagnosis').value,
                treatmentPlan: document.getElementById('treatmentPlan').value,
                providerName: document.getElementById('providerName').value,
                providerNPI: document.getElementById('providerNPI').value
            };

            const jsonData = JSON.stringify(formData, null, 2);
            const blob = new Blob([jsonData], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `insurance_form_${new Date().toISOString().split('T')[0]}.json`;
            a.click();
            URL.revokeObjectURL(url);
            
            showStatus('Form exported successfully!', 'success');
        }

        function clearForm() {
            document.getElementById('insuranceForm').reset();
            showStatus('Form cleared', 'info');
        }
    </script>
</body>
</html>
