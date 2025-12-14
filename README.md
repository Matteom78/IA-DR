<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Générateur d'Images IA</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
            max-width: 1200px;
            width: 100%;
            padding: 40px;
            margin: 0 auto;
        }

        h1 {
            text-align: center;
            color: #667eea;
            margin-bottom: 10px;
            font-size: 2.5em;
        }

        .subtitle {
            text-align: center;
            color: #666;
            margin-bottom: 30px;
            font-size: 1.1em;
        }

        .badge {
            display: inline-block;
            background: #4CAF50;
            color: white;
            padding: 5px 15px;
            border-radius: 20px;
            font-size: 0.9em;
            margin-left: 10px;
        }

        .input-section {
            margin-bottom: 30px;
        }

        textarea {
            width: 100%;
            padding: 15px;
            border: 2px solid #ddd;
            border-radius: 10px;
            font-size: 16px;
            resize: vertical;
            min-height: 100px;
            transition: border-color 0.3s;
            font-family: inherit;
        }

        textarea:focus {
            outline: none;
            border-color: #667eea;
        }

        .options {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-top: 15px;
        }

        .option-group {
            display: flex;
            flex-direction: column;
        }

        label {
            font-weight: bold;
            color: #555;
            margin-bottom: 5px;
            font-size: 14px;
        }

        select {
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 14px;
            cursor: pointer;
            transition: border-color 0.3s;
        }

        select:focus {
            outline: none;
            border-color: #667eea;
        }

        .button-group {
            display: flex;
            gap: 10px;
            margin-top: 15px;
        }

        button {
            flex: 1;
            padding: 15px 30px;
            font-size: 18px;
            font-weight: bold;
            border: none;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.3s;
        }

        .generate-btn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .generate-btn:hover:not(:disabled) {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(102, 126, 234, 0.4);
        }

        .generate-btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }

        .clear-btn {
            background: #f44336;
            color: white;
            flex: 0.3;
        }

        .clear-btn:hover {
            background: #da190b;
        }

        .loading {
            text-align: center;
            margin: 20px 0;
            color: #667eea;
            font-size: 18px;
            display: none;
        }

        .loading.active {
            display: block;
        }

        .spinner {
            border: 4px solid #f3f3f3;
            border-top: 4px solid #667eea;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 0 auto 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .gallery {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            margin-top: 30px;
        }

        .image-card {
            background: white;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s;
        }

        .image-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
        }

        .image-wrapper {
            width: 100%;
            height: 300px;
            overflow: hidden;
            background: #f0f0f0;
            position: relative;
        }

        .image-card img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: transform 0.3s;
        }

        .image-card:hover img {
            transform: scale(1.05);
        }

        .image-loading {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }

        .image-info {
            padding: 15px;
        }

        .image-prompt {
            font-size: 14px;
            color: #666;
            margin-bottom: 10px;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }

        .image-actions {
            display: flex;
            gap: 10px;
        }

        .action-btn {
            flex: 1;
            padding: 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.3s;
        }

        .download-btn {
            background: #4CAF50;
            color: white;
        }

        .download-btn:hover {
            background: #45a049;
        }

        .view-btn {
            background: #2196F3;
            color: white;
        }

        .view-btn:hover {
            background: #0b7dda;
        }

        .counter {
            text-align: center;
            margin: 20px 0;
            font-size: 18px;
            color: #667eea;
            font-weight: bold;
        }

        .error {
            background: #ffebee;
            color: #c62828;
            padding: 15px;
            border-radius: 10px;
            margin: 20px 0;
            display: none;
        }

        .error.active {
            display: block;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🎨 Générateur d'Images IA <span class="badge">GRATUIT</span></h1>
        <p class="subtitle">Génération illimitée avec Pollinations.ai - Décris ton image et l'IA la créera !</p>

        <div class="input-section">
            <textarea id="promptInput" placeholder="Décris l'image que tu veux créer en détail...

Exemples:
- Un chat astronaute flottant dans l'espace avec des étoiles colorées
- Un dragon majestueux sur une montagne enneigée au coucher du soleil
- Une ville futuriste avec des voitures volantes et des gratte-ciels lumineux
- Un jardin magique avec des fleurs brillantes et des papillons arc-en-ciel"></textarea>
            
            <div class="options">
                <div class="option-group">
                    <label for="width">Largeur</label>
                    <select id="width">
                        <option value="512">512px</option>
                        <option value="768">768px</option>
                        <option value="1024" selected>1024px</option>
                        <option value="1280">1280px</option>
                    </select>
                </div>
                <div class="option-group">
                    <label for="height">Hauteur</label>
                    <select id="height">
                        <option value="512">512px</option>
                        <option value="768">768px</option>
                        <option value="1024" selected>1024px</option>
                        <option value="1280">1280px</option>
                    </select>
                </div>
                <div class="option-group">
                    <label for="model">Modèle IA</label>
                    <select id="model">
                        <option value="flux">Flux (Rapide)</option>
                        <option value="flux-realism">Flux Réaliste</option>
                        <option value="flux-anime">Flux Anime</option>
                        <option value="flux-3d">Flux 3D</option>
                    </select>
                </div>
            </div>

            <div class="button-group">
                <button class="generate-btn" id="generateBtn">✨ Générer l'Image</button>
                <button class="clear-btn" id="clearBtn">🗑️ Effacer</button>
            </div>
        </div>

        <div class="loading" id="loading">
            <div class="spinner"></div>
            <p>Génération de ton image en cours...</p>
        </div>

        <div class="error" id="error"></div>

        <div class="counter" id="counter">0 images générées</div>

        <div class="gallery" id="gallery"></div>
    </div>

    <script>
        const promptInput = document.getElementById('promptInput');
        const generateBtn = document.getElementById('generateBtn');
        const clearBtn = document.getElementById('clearBtn');
        const loading = document.getElementById('loading');
        const gallery = document.getElementById('gallery');
        const errorDiv = document.getElementById('error');
        const counter = document.getElementById('counter');
        const widthSelect = document.getElementById('width');
        const heightSelect = document.getElementById('height');
        const modelSelect = document.getElementById('model');

        let imageCount = 0;

        generateBtn.addEventListener('click', generateImage);
        clearBtn.addEventListener('click', clearGallery);

        promptInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                generateImage();
            }
        });

        async function generateImage() {
            const prompt = promptInput.value.trim();
            
            if (!prompt) {
                showError('Merci de décrire l\'image que tu veux créer !');
                return;
            }

            hideError();
            loading.classList.add('active');
            generateBtn.disabled = true;

            try {
                const width = widthSelect.value;
                const height = heightSelect.value;
                const model = modelSelect.value;
                
                // Encoder le prompt pour l'URL
                const encodedPrompt = encodeURIComponent(prompt);
                
                // URL de l'API Pollinations.ai
                const imageUrl = `https://image.pollinations.ai/prompt/${encodedPrompt}?width=${width}&height=${height}&model=${model}&nologo=true`;
                
                addImageToGallery(imageUrl, prompt);
                imageCount++;
                updateCounter();
                
                promptInput.value = '';

            } catch (error) {
                console.error('Erreur:', error);
                showError('Oups ! Une erreur est survenue. Réessaie !');
            } finally {
                loading.classList.remove('active');
                generateBtn.disabled = false;
            }
        }

        function addImageToGallery(imageUrl, prompt) {
            const imageCard = document.createElement('div');
            imageCard.className = 'image-card';
            
            imageCard.innerHTML = `
                <div class="image-wrapper">
                    <div class="image-loading">
                        <div class="spinner"></div>
                    </div>
                    <img data-src="${imageUrl}" alt="${prompt}" style="display: none;">
                </div>
                <div class="image-info">
                    <div class="image-prompt">${prompt}</div>
                    <div class="image-actions">
                        <button class="action-btn download-btn">⬇️ Télécharger</button>
                        <button class="action-btn view-btn">👁️ Voir</button>
                    </div>
                </div>
            `;
            
            const img = imageCard.querySelector('img');
            const loadingDiv = imageCard.querySelector('.image-loading');
            const downloadBtn = imageCard.querySelector('.download-btn');
            const viewBtn = imageCard.querySelector('.view-btn');
            
            // Charger l'image
            img.onload = () => {
                loadingDiv.style.display = 'none';
                img.style.display = 'block';
            };
            
            img.onerror = () => {
                loadingDiv.innerHTML = '<p style="color: red;">Erreur de chargement</p>';
            };
            
            // Commencer le chargement
            img.src = img.dataset.src;
            
            // Bouton télécharger
            downloadBtn.addEventListener('click', () => downloadImage(imageUrl, prompt));
            
            // Bouton voir (ouvrir dans un nouvel onglet)
            viewBtn.addEventListener('click', () => window.open(imageUrl, '_blank'));
            
            gallery.insertBefore(imageCard, gallery.firstChild);
        }

        async function downloadImage(url, prompt) {
            try {
                const response = await fetch(url);
                const blob = await response.blob();
                const blobUrl = URL.createObjectURL(blob);
                
                const a = document.createElement('a');
                a.href = blobUrl;
                a.download = `${prompt.substring(0, 30).replace(/[^a-z0-9]/gi, '_')}.png`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(blobUrl);
            } catch (error) {
                console.error('Erreur de téléchargement:', error);
                showError('Erreur lors du téléchargement. Essaie avec le bouton "Voir" puis clique droit > Enregistrer.');
            }
        }

        function clearGallery() {
            if (imageCount === 0) return;
            
            if (confirm('Es-tu sûr de vouloir effacer toutes les images ?')) {
                gallery.innerHTML = '';
                imageCount = 0;
                updateCounter();
            }
        }

        function updateCounter() {
            counter.textContent = `${imageCount} image${imageCount > 1 ? 's' : ''} générée${imageCount > 1 ? 's' : ''}`;
        }

        function showError(message) {
            errorDiv.textContent = message;
            errorDiv.classList.add('active');
            setTimeout(() => hideError(), 5000);
        }

        function hideError() {
            errorDiv.classList.remove('active');
        }
    </script>
</body>
</html>
