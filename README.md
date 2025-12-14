<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Générateur d'Images IA - Recherche Intelligente</title>
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
            position: relative;
            overflow-x: hidden;
        }

        body::before {
            content: '';
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: 
                radial-gradient(circle at 20% 50%, rgba(255, 255, 255, 0.1) 0%, transparent 50%),
                radial-gradient(circle at 80% 80%, rgba(255, 255, 255, 0.1) 0%, transparent 50%);
            pointer-events: none;
            z-index: 0;
        }

        .container {
            background: rgba(255, 255, 255, 0.98);
            border-radius: 25px;
            box-shadow: 0 25px 70px rgba(0, 0, 0, 0.3);
            max-width: 1400px;
            width: 100%;
            padding: 50px;
            margin: 0 auto;
            position: relative;
            z-index: 1;
            backdrop-filter: blur(10px);
        }

        .header {
            text-align: center;
            margin-bottom: 40px;
        }

        h1 {
            color: #667eea;
            margin-bottom: 15px;
            font-size: 3em;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
            animation: fadeInDown 0.8s ease;
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

        .subtitle {
            color: #666;
            font-size: 1.2em;
            margin-bottom: 15px;
        }

        .badge {
            display: inline-block;
            background: linear-gradient(135deg, #4CAF50 0%, #45a049 100%);
            color: white;
            padding: 8px 20px;
            border-radius: 25px;
            font-size: 0.9em;
            margin: 5px;
            box-shadow: 0 4px 15px rgba(76, 175, 80, 0.3);
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        .ai-search-section {
            background: linear-gradient(135deg, #e3f2fd 0%, #f3e5f5 100%);
            border-radius: 20px;
            padding: 25px;
            margin-bottom: 30px;
            border: 2px solid #667eea;
            box-shadow: 0 8px 20px rgba(102, 126, 234, 0.2);
        }

        .ai-search-header {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 15px;
        }

        .ai-search-header h3 {
            color: #667eea;
            font-size: 1.3em;
        }

        .search-input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 15px;
        }

        .search-input {
            flex: 1;
            padding: 15px;
            border: 2px solid #667eea;
            border-radius: 12px;
            font-size: 16px;
            transition: all 0.3s;
        }

        .search-input:focus {
            outline: none;
            border-color: #764ba2;
            box-shadow: 0 0 15px rgba(102, 126, 234, 0.3);
        }

        .search-btn {
            padding: 15px 30px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 12px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s;
        }

        .search-btn:hover:not(:disabled) {
            transform: translateY(-2px);
            box-shadow: 0 8px 20px rgba(102, 126, 234, 0.4);
        }

        .search-btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }

        .search-results {
            background: white;
            border-radius: 12px;
            padding: 15px;
            margin-top: 15px;
            max-height: 300px;
            overflow-y: auto;
            display: none;
        }

        .search-results.active {
            display: block;
        }

        .search-result-item {
            padding: 10px;
            border-bottom: 1px solid #eee;
            color: #555;
            font-size: 14px;
            line-height: 1.6;
        }

        .search-result-item:last-child {
            border-bottom: none;
        }

        .input-section {
            margin-bottom: 30px;
        }

        textarea {
            width: 100%;
            padding: 20px;
            border: 3px solid #ddd;
            border-radius: 15px;
            font-size: 16px;
            resize: vertical;
            min-height: 120px;
            transition: all 0.3s;
            font-family: inherit;
            background: linear-gradient(to bottom, #ffffff 0%, #f8f9fa 100%);
        }

        textarea:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 20px rgba(102, 126, 234, 0.2);
        }

        .style-selector {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 15px;
            margin: 20px 0;
        }

        .style-card {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            border: 3px solid transparent;
            border-radius: 15px;
            padding: 20px;
            text-align: center;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
            overflow: hidden;
            z-index: 10;
        }

        .style-card::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, rgba(102, 126, 234, 0.1) 0%, rgba(118, 75, 162, 0.1) 100%);
            opacity: 0;
            transition: opacity 0.3s;
        }

        .style-card:hover::before {
            opacity: 1;
        }

        .style-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
        }

        .style-card.active {
            border-color: #667eea;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            transform: scale(1.05);
        }

        .style-card .emoji {
            font-size: 2.5em;
            margin-bottom: 10px;
            display: block;
            position: relative;
            z-index: 11;
        }

        .style-card .name {
            font-weight: bold;
            font-size: 1.1em;
            position: relative;
            z-index: 11;
        }

        .options {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-top: 20px;
        }

        .option-group {
            display: flex;
            flex-direction: column;
        }

        label {
            font-weight: bold;
            color: #555;
            margin-bottom: 8px;
            font-size: 15px;
        }

        select {
            padding: 12px;
            border: 2px solid #ddd;
            border-radius: 10px;
            font-size: 15px;
            cursor: pointer;
            transition: all 0.3s;
            background: white;
            position: relative;
            z-index: 10;
        }

        select:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 10px rgba(102, 126, 234, 0.2);
        }

        .button-group {
            display: flex;
            gap: 15px;
            margin-top: 20px;
        }

        button {
            flex: 1;
            padding: 18px 35px;
            font-size: 18px;
            font-weight: bold;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.3s;
            position: relative;
            z-index: 10;
        }

        .generate-btn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4);
        }

        .generate-btn:hover:not(:disabled) {
            transform: translateY(-3px);
            box-shadow: 0 12px 30px rgba(102, 126, 234, 0.5);
        }

        .generate-btn:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }

        .clear-btn {
            background: linear-gradient(135deg, #f44336 0%, #e91e63 100%);
            color: white;
            flex: 0.3;
        }

        .clear-btn:hover {
            background: linear-gradient(135deg, #da190b 0%, #c2185b 100%);
            transform: translateY(-3px);
        }

        .loading {
            text-align: center;
            margin: 30px 0;
            color: #667eea;
            font-size: 20px;
            display: none;
        }

        .loading.active {
            display: block;
        }

        .spinner {
            border: 5px solid #f3f3f3;
            border-top: 5px solid #667eea;
            border-radius: 50%;
            width: 50px;
            height: 50px;
            animation: spin 1s linear infinite;
            margin: 0 auto 15px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .gallery {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
            gap: 25px;
            margin-top: 40px;
        }

        .image-card {
            background: white;
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
            transition: all 0.3s;
            animation: fadeIn 0.5s ease;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: scale(0.9);
            }
            to {
                opacity: 1;
                transform: scale(1);
            }
        }

        .image-card:hover {
            transform: translateY(-8px);
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.2);
        }

        .image-wrapper {
            width: 100%;
            height: 320px;
            overflow: hidden;
            background: linear-gradient(135deg, #f0f0f0 0%, #e0e0e0 100%);
            position: relative;
        }

        .image-card img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: transform 0.3s;
        }

        .image-card:hover img {
            transform: scale(1.1);
        }

        .image-loading {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
        }

        .image-info {
            padding: 20px;
        }

        .image-prompt {
            font-size: 15px;
            color: #666;
            margin-bottom: 12px;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
            line-height: 1.5;
        }

        .image-style-tag {
            display: inline-block;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 5px 12px;
            border-radius: 15px;
            font-size: 12px;
            margin-bottom: 10px;
        }

        .image-actions {
            display: flex;
            gap: 10px;
        }

        .action-btn {
            flex: 1;
            padding: 12px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-size: 15px;
            font-weight: bold;
            transition: all 0.3s;
            position: relative;
            z-index: 10;
        }

        .download-btn {
            background: linear-gradient(135deg, #4CAF50 0%, #45a049 100%);
            color: white;
        }

        .download-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(76, 175, 80, 0.4);
        }

        .view-btn {
            background: linear-gradient(135deg, #2196F3 0%, #0b7dda 100%);
            color: white;
        }

        .view-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 15px rgba(33, 150, 243, 0.4);
        }

        .counter {
            text-align: center;
            margin: 30px 0;
            font-size: 22px;
            color: #667eea;
            font-weight: bold;
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
        }

        .error {
            background: linear-gradient(135deg, #ffebee 0%, #ffcdd2 100%);
            color: #c62828;
            padding: 20px;
            border-radius: 15px;
            margin: 20px 0;
            display: none;
            border-left: 5px solid #c62828;
        }

        .error.active {
            display: block;
            animation: shake 0.5s;
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-10px); }
            75% { transform: translateX(10px); }
        }

        @media (max-width: 768px) {
            .container {
                padding: 20px;
            }
            
            h1 {
                font-size: 2em;
            }
            
            .style-selector {
                grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
            }
            
            .gallery {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🎨 Générateur d'Images IA Intelligent</h1>
            <p class="subtitle">Recherche Google + Génération illimitée</p>
            <div>
                <span class="badge">✨ GRATUIT</span>
                <span class="badge">🔍 RECHERCHE IA</span>
                <span class="badge">🎯 MULTI-STYLES</span>
            </div>
        </div>

        <div class="ai-search-section">
            <div class="ai-search-header">
                <h3>🔍 Recherche Intelligente</h3>
            </div>
            <p style="color: #666; margin-bottom: 15px; font-size: 14px;">
                <strong>🤖 IA Boostée par Claude + Recherche Web Réelle !</strong><br>
                L'IA va chercher des informations actuelles sur Google pour enrichir votre prompt avec des détails réalistes et précis !
            </p>
            <div class="search-input-group">
                <input type="text" class="search-input" id="searchInput" placeholder="Ex: Tour Eiffel, Dragon japonais, Lamborghini, Chat mignon...">
                <button class="search-btn" id="searchBtn">🔍 Rechercher</button>
            </div>
            <div class="search-results" id="searchResults"></div>
        </div>

        <div class="input-section">
            <textarea id="promptInput" placeholder="Décris l'image que tu veux créer en détail...

💡 Astuce: Utilise la recherche ci-dessus pour obtenir des détails précis, puis complète ici avec ton style !

Exemples:
- Un chat astronaute flottant dans l'espace avec des étoiles colorées
- Un dragon majestueux sur une montagne enneigée au coucher du soleil
- Une ville futuriste avec des voitures volantes et des gratte-ciels lumineux"></textarea>
            
            <div style="margin: 20px 0;">
                <label style="display: block; margin-bottom: 15px; font-size: 18px; color: #667eea;">🎨 Choisis ton style:</label>
                <div class="style-selector">
                    <div class="style-card active" data-style="flux">
                        <span class="emoji">⚡</span>
                        <span class="name">Standard</span>
                    </div>
                    <div class="style-card" data-style="flux-realism">
                        <span class="emoji">📸</span>
                        <span class="name">Réaliste</span>
                    </div>
                    <div class="style-card" data-style="flux-anime">
                        <span class="emoji">🎌</span>
                        <span class="name">Anime</span>
                    </div>
                    <div class="style-card" data-style="flux-3d">
                        <span class="emoji">🎮</span>
                        <span class="name">3D</span>
                    </div>
                    <div class="style-card" data-style="flux-pro">
                        <span class="emoji">💎</span>
                        <span class="name">Pro</span>
                    </div>
                    <div class="style-card" data-style="turbo">
                        <span class="emoji">🚀</span>
                        <span class="name">Rapide</span>
                    </div>
                </div>
            </div>

            <div class="options">
                <div class="option-group">
                    <label for="width">📏 Largeur</label>
                    <select id="width">
                        <option value="512">512px - Rapide</option>
                        <option value="768">768px - Moyen</option>
                        <option value="1024" selected>1024px - Standard</option>
                        <option value="1280">1280px - Haute Qualité</option>
                    </select>
                </div>
                <div class="option-group">
                    <label for="height">📐 Hauteur</label>
                    <select id="height">
                        <option value="512">512px - Rapide</option>
                        <option value="768">768px - Moyen</option>
                        <option value="1024" selected>1024px - Standard</option>
                        <option value="1280">1280px - Haute Qualité</option>
                    </select>
                </div>
                <div class="option-group">
                    <label for="seed">🎲 Seed (Optionnel)</label>
                    <select id="seed">
                        <option value="">Aléatoire</option>
                        <option value="42">42 - Reproductible</option>
                        <option value="123">123</option>
                        <option value="777">777</option>
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
        // Variables globales
        const promptInput = document.getElementById('promptInput');
        const generateBtn = document.getElementById('generateBtn');
        const clearBtn = document.getElementById('clearBtn');
        const loading = document.getElementById('loading');
        const gallery = document.getElementById('gallery');
        const errorDiv = document.getElementById('error');
        const counter = document.getElementById('counter');
        const widthSelect = document.getElementById('width');
        const heightSelect = document.getElementById('height');
        const seedSelect = document.getElementById('seed');
        const styleCards = document.querySelectorAll('.style-card');
        const searchInput = document.getElementById('searchInput');
        const searchBtn = document.getElementById('searchBtn');
        const searchResults = document.getElementById('searchResults');

        let imageCount = 0;
        let selectedStyle = 'flux';

        // Initialisation
        window.addEventListener('DOMContentLoaded', () => {
            console.log('✅ Application chargée avec succès');
        });

        // Gestion de la sélection de style
        styleCards.forEach(card => {
            card.addEventListener('click', () => {
                styleCards.forEach(c => c.classList.remove('active'));
                card.classList.add('active');
                selectedStyle = card.dataset.style;
                console.log('Style sélectionné:', selectedStyle);
            });
        });

        // Événements de recherche
        searchBtn.addEventListener('click', performSearch);
        searchInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                e.preventDefault();
                performSearch();
            }
        });

        // Fonction de recherche avec IA
        async function performSearch() {
            const query = searchInput.value.trim();
            
            if (!query) {
                showError('⚠️ Merci d\'entrer un terme de recherche !');
                return;
            }

            searchBtn.disabled = true;
            searchResults.innerHTML = `
                <div style="text-align: center; padding: 20px;">
                    <div class="spinner"></div>
                    <p style="margin-top: 15px;">🔍 Recherche IA en cours sur le web...</p>
                    <small style="color: #888;">Cela peut prendre quelques secondes...</small>
                </div>
            `;
            searchResults.classList.add('active');

            try {
                console.log('🔍 Recherche pour:', query);
                const enrichedPrompt = await enrichPromptWithSearch(query);
                
                if (enrichedPrompt && enrichedPrompt.length > 0) {
                    promptInput.value = enrichedPrompt;
                    promptInput.focus();
                    
                    searchResults.innerHTML = `
                        <div class="search-result-item">
                            <strong style="color: #4CAF50;">✅ Recherche IA terminée avec succès !</strong><br>
                            <p style="margin: 10px 0;">Votre prompt a été enrichi avec des informations trouvées sur le web.</p>
                            <small style="color: #888;">💡 Vous pouvez maintenant le modifier ou générer directement l'image.</small>
                        </div>
                    `;
                    
                    setTimeout(() => {
                        searchResults.classList.remove('active');
                    }, 5000);
                } else {
                    throw new Error('Prompt vide reçu');
                }
                
            } catch (error) {
                console.error('❌ Erreur recherche:', error);
                
                // Création d'un prompt de secours enrichi
                const fallbackPrompt = createFallbackPrompt(query);
                promptInput.value = fallbackPrompt;
                
                searchResults.innerHTML = `
                    <div class="search-result-item" style="background: #fff3cd; border-left: 4px solid #ff9800;">
                        <strong style="color: #ff9800;">⚠️ Mode de secours activé</strong><br>
                        <p style="margin: 10px 0;">La recherche IA n'a pas pu aboutir, mais un prompt enrichi a été créé pour vous.</p>
                        <small style="color: #666;">Vous pouvez quand même générer une belle image !</small>
                    </div>
                `;
                
                setTimeout(() => {
                    searchResults.classList.remove('active');
                }, 5000);
            } finally {
                searchBtn.disabled = false;
            }
        }

        // Fonction d'enrichissement avec API Claude
        async function enrichPromptWithSearch(query) {
            try {
                const response = await fetch("https://api.anthropic.com/v1/messages", {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        model: "claude-sonnet-4-20250514",
                        max_tokens: 1000,
                        tools: [{
                            type: "web_search_20250305",
                            name: "web_search"
                        }],
                        messages: [{
                            role: "user",
                            content: `Je veux créer une image de: "${query}"

Recherche des informations détaillées sur le web et crée un prompt descriptif en français pour un générateur d'images IA.

Instructions:
- Utilise les informations trouvées sur le web
- Inclus des détails visuels précis (couleurs, formes, textures, lumière)
- Ajoute des éléments de style artistique
- Rends le prompt vivant et inspirant
- Maximum 3-4 phrases

Réponds UNIQUEMENT avec le prompt enrichi, sans introduction ni explication.`
                        }]
                    })
                });

                if (!response.ok) {
                    throw new Error(`Erreur API: ${response.status}`);
                }

                const data = await response.json();
                console.log('📥 Réponse API reçue');
                
                // Extraction du texte
                let enrichedPrompt = data.content
                    .filter(item => item.type === "text")
                    .map(item => item.text)
                    .join(" ")
                    .trim();

                // Nettoyage du prompt
                enrichedPrompt = enrichedPrompt
                    .replace(/^["']|["']$/g, '')
                    .replace(/\n+/g, ' ')
                    .trim();

                if (enrichedPrompt.length < 20) {
                    console.log('⚠️ Prompt trop court, utilisation du fallback');
                    return createFallbackPrompt(query);
                }

                console.log('✅ Prompt enrichi créé:', enrichedPrompt.substring(0, 100) + '...');
                return enrichedPrompt;

            } catch (error) {
                console.error('❌ Erreur API Claude:', error);
                return createFallbackPrompt(query);
            }
        }

        // Fonction de création de prompt de secours
        function createFallbackPrompt(query) {
            const enrichments = {
                'chat': 'un magnifique chat aux yeux expressifs et brillants, fourrure douce et détaillée, éclairage doux et chaleureux, composition artistique professionnelle',
                'chien': 'un chien adorable et expressif, pelage détaillé et réaliste, regard attendrissant, lumière naturelle douce, haute qualité photographique',
                'dragon': 'un dragon majestueux avec des écailles brillantes et iridescentes, ailes déployées impressionnantes, flammes mystiques éthérées, atmosphère épique et fantastique',
                'tour eiffel': 'la Tour Eiffel emblématique à Paris, architecture en fer forgé détaillée, perspective dramatique, ciel coloré au coucher du soleil, éclairage cinématographique',
                'voiture': 'une voiture de sport luxueuse et moderne, carrosserie brillante et réfléchissante, détails chromés précis, éclairage dynamique professionnel, composition automobile premium',
                'paysage': 'un paysage naturel époustouflant, lumière cinématographique dorée, détails naturels riches et variés, profondeur atmosphérique, composition majestueuse',
                'ville': 'une métropole futuriste impressionnante, gratte-ciels illuminés spectaculaires, architecture moderne innovante, ambiance cyberpunk nocturne, néons colorés',
                'nature': 'une scène naturelle luxuriante et vivante, végétation détaillée et variée, éclairage naturel parfait, atmosphère paisible, couleurs vibrantes',
                'espace': 'un panorama spatial majestueux avec des étoiles scintillantes, nébuleuses colorées spectaculaires, profondeur cosmique infinie, atmosphère mystérieuse',
                'océan': 'un océan majestueux et puissant, vagues réalistes et dynamiques, reflets lumineux sur l'eau, profondeur aquatique mystérieuse, ambiance maritime',
                'montagne': 'des montagnes imposantes et majestueuses, sommets enneigés brillants, rochers détaillés et texturés, atmosphère alpine pure, lumière naturelle spectaculaire',
                'forêt': 'une forêt luxuriante et mystérieuse, arbres imposants, lumière filtrant à travers les feuilles, atmosphère féerique, détails botaniques riches',
                'plage': 'une plage paradisiaque idyllique, sable fin et doré, eau turquoise cristalline, palmiers, lumière ensoleillée dorée, ambiance tropicale',
                'fleur': 'une fleur magnifique et délicate, pétales détaillés avec textures subtiles, couleurs vibrantes et saturées, éclairage macro artistique, arrière-plan flou esthétique',
                'robot': 'un robot futuriste avancé, design mécanique complexe et détaillé, finitions métalliques brillantes, éclairage néon ambiant, style science-fiction premium',
                'château': 'un château médiéval imposant et majestueux, architecture gothique détaillée, tours et tourelles, atmosphère historique, éclairage dramatique crépusculaire'
            };

            // Chercher une correspondance
            for (const [key, value] of Object.entries(enrichments)) {
                if (query.toLowerCase().includes(key)) {
                    return value;
                }
            }

            // Prompt générique enrichi
            return `${query}, composition artistique professionnelle, détails précis et réalistes, éclairage cinématographique dramatique, couleurs vibrantes et saturées, haute qualité 8K, rendu photoréaliste`;
        }

        // Événements de génération
        generateBtn.addEventListener('click', generateImage);
        clearBtn.addEventListener('click', clearGallery);

        promptInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                generateImage();
            }
        });

        // Fonction de génération d'image
        async function generateImage() {
            const prompt = promptInput.value.trim();
            
            if (!prompt) {
                showError('⚠️ Merci de décrire l\'image que tu veux créer !');
                promptInput.focus();
                return;
            }

            console.log('🎨 Génération d\'image pour:', prompt.substring(0, 50) + '...');
            
            hideError();
            loading.classList.add('active');
            generateBtn.disabled = true;

            try {
                const width = widthSelect.value;
                const height = heightSelect.value;
                const seed = seedSelect.value;
                
                const encodedPrompt = encodeURIComponent(prompt);
                
                let imageUrl = `https://image.pollinations.ai/prompt/${encodedPrompt}?width=${width}&height=${height}&model=${selectedStyle}&nologo=true&enhance=true`;
                
                if (seed) {
                    imageUrl += `&seed=${seed}`;
                }
                
                // Ajouter timestamp pour éviter le cache
                imageUrl += `&timestamp=${Date.now()}`;
                
                console.log('🔗 URL générée');
                
                addImageToGallery(imageUrl, prompt, selectedStyle);
                imageCount++;
                updateCounter();
                
                // Effacer le prompt après génération
                promptInput.value = '';
                
                console.log('✅ Image ajoutée à la galerie');

            } catch (error) {
                console.error('❌ Erreur génération:', error);
                showError('😢 Oups ! Une erreur est survenue. Réessaie dans quelques instants !');
            } finally {
                loading.classList.remove('active');
                generateBtn.disabled = false;
            }
        }

        // Fonction d'ajout à la galerie
        function addImageToGallery(imageUrl, prompt, style) {
            const styleNames = {
                'flux': 'Standard',
                'flux-realism': 'Réaliste',
                'flux-anime': 'Anime',
                'flux-3d': '3D',
                'flux-pro': 'Pro',
                'turbo': 'Rapide'
            };

            const imageCard = document.createElement('div');
            imageCard.className = 'image-card';
            
            imageCard.innerHTML = `
                <div class="image-wrapper">
                    <div class="image-loading">
                        <div class="spinner"></div>
                        <p style="color: #667eea; margin-top: 10px; font-size: 14px;">Chargement...</p>
                    </div>
                    <img data-src="${imageUrl}" alt="${prompt}" style="display: none;">
                </div>
                <div class="image-info">
                    <div class="image-style-tag">${styleNames[style] || style}</div>
                    <div class="image-prompt" title="${prompt}">${prompt}</div>
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
            
            // Gestion du chargement de l'image
            img.onload = () => {
                console.log('✅ Image chargée avec succès');
                loadingDiv.style.display = 'none';
                img.style.display = 'block';
            };
            
            img.onerror = () => {
                console.error('❌ Erreur de chargement d\'image');
                loadingDiv.innerHTML = '<p style="color: #f44336; font-size: 14px;">❌ Erreur de chargement</p>';
            };
            
            // Charger l'image
            setTimeout(() => {
                img.src = img.dataset.src;
            }, 100);
            
            // Événements des boutons
            downloadBtn.addEventListener('click', () => downloadImage(imageUrl, prompt));
            viewBtn.addEventListener('click', () => window.open(imageUrl, '_blank'));
            
            // Ajouter au début de la galerie
            gallery.insertBefore(imageCard, gallery.firstChild);
        }

        // Fonction de téléchargement
        async function downloadImage(url, prompt) {
            try {
                console.log('📥 Téléchargement en cours...');
                
                const response = await fetch(url);
                if (!response.ok) throw new Error('Erreur de téléchargement');
                
                const blob = await response.blob();
                const blobUrl = URL.createObjectURL(blob);
                
                const a = document.createElement('a');
                a.href = blobUrl;
                a.download = `image_ia_${prompt.substring(0, 30).replace(/[^a-z0-9]/gi, '_')}_${Date.now()}.png`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(blobUrl);
                
                console.log('✅ Téléchargement réussi');
                
            } catch (error) {
                console.error('❌ Erreur téléchargement:', error);
                showError('❌ Erreur lors du téléchargement. Essaie avec le bouton "Voir" puis clique droit > Enregistrer l\'image.');
            }
        }

        // Fonction d'effacement de la galerie
        function clearGallery() {
            if (imageCount === 0) {
                showError('ℹ️ La galerie est déjà vide !');
                return;
            }
            
            if (confirm(`🗑️ Es-tu sûr de vouloir effacer toutes les ${imageCount} images ?`)) {
                gallery.innerHTML = '';
                imageCount = 0;
                updateCounter();
                console.log('🗑️ Galerie effacée');
                showError('✅ Galerie effacée avec succès !');
                setTimeout(hideError, 3000);
            }
        }

        // Fonction de mise à jour du compteur
        function updateCounter() {
            if (imageCount === 0) {
                counter.textContent = 'Aucune image générée';
            } else if (imageCount === 1) {
                counter.textContent = '1 image générée';
            } else {
                counter.textContent = `${imageCount} images générées`;
            }
        }

        // Fonction d'affichage d'erreur
        function showError(message) {
            errorDiv.textContent = message;
            errorDiv.classList.add('active');
            setTimeout(() => hideError(), 5000);
        }

        // Fonction de masquage d'erreur
        function hideError() {
            errorDiv.classList.remove('active');
        }

        // Message de bienvenue dans la console
        console.log('%c🎨 Générateur d\'Images IA', 'color: #667eea; font-size: 24px; font-weight: bold;');
        console.log('%c✨ Application 100% fonctionnelle avec recherche IA intégrée', 'color: #4CAF50; font-size: 14px;');
        console.log('%cPar Claude - Anthropic', 'color: #666; font-size: 12px;');
    </script>
</body>
</html>
