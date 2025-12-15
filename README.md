import React, { useState, useRef, useEffect } from 'react';
import { Send, Globe, Code, Sparkles, Loader2 } from 'lucide-react';

export default function AICodeAssistant() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);
  const messagesEndRef = useRef(null);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  const handleSubmit = async (e) => {
    e.preventDefault();
    if (!input.trim() || loading) return;

    const userMessage = input.trim();
    setInput('');
    setMessages(prev => [...prev, { role: 'user', content: userMessage }]);
    setLoading(true);

    try {
      const response = await fetch("https://api.anthropic.com/v1/messages", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          model: "claude-sonnet-4-20250514",
          max_tokens: 4000,
          system: `Tu es un assistant IA expert en programmation. Tu as accès à la recherche web pour trouver des informations actuelles.

Capacités:
- Utilise l'outil web_search pour chercher des informations sur Internet quand nécessaire
- Tu es expert en tous les langages de programmation
- Tu donnes des explications claires et du code de qualité
- Tu utilises la recherche web pour trouver des solutions à jour, des documentations, ou vérifier des informations

Quand utiliser la recherche web:
- Pour trouver des informations récentes (versions de librairies, nouvelles API, etc.)
- Pour vérifier la documentation officielle
- Pour trouver des solutions à des problèmes spécifiques
- Pour découvrir les meilleures pratiques actuelles

Réponds toujours en français de manière claire et professionnelle.`,
          messages: [{ role: "user", content: userMessage }],
          tools: [{
            type: "web_search_20250305",
            name: "web_search"
          }]
        })
      });

      const data = await response.json();
      
      // Gérer les réponses avec tool use
      let fullResponse = '';
      let hasToolUse = false;
      
      for (const block of data.content) {
        if (block.type === 'text') {
          fullResponse += block.text;
        } else if (block.type === 'tool_use') {
          hasToolUse = true;
          setMessages(prev => [...prev, { 
            role: 'assistant', 
            content: '🔍 Recherche en cours sur le web...',
            isSearching: true 
          }]);
        }
      }

      // Si l'IA a utilisé des outils, faire un second appel pour obtenir la réponse finale
      if (hasToolUse && data.stop_reason === 'tool_use') {
        const toolResults = data.content
          .filter(block => block.type === 'tool_use')
          .map(block => ({
            type: "tool_result",
            tool_use_id: block.id,
            content: "Recherche effectuée avec succès."
          }));

        const followUpResponse = await fetch("https://api.anthropic.com/v1/messages", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({
            model: "claude-sonnet-4-20250514",
            max_tokens: 4000,
            system: `Tu es un assistant IA expert en programmation avec accès à la recherche web.`,
            messages: [
              { role: "user", content: userMessage },
              { role: "assistant", content: data.content },
              { role: "user", content: toolResults }
            ]
          })
        });

        const followUpData = await followUpResponse.json();
        fullResponse = followUpData.content
          .filter(block => block.type === 'text')
          .map(block => block.text)
          .join('\n');
      }

      setMessages(prev => prev.filter(m => !m.isSearching));
      setMessages(prev => [...prev, { role: 'assistant', content: fullResponse }]);
    } catch (error) {
      setMessages(prev => [...prev, { 
        role: 'assistant', 
        content: `❌ Erreur: ${error.message}` 
      }]);
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-slate-900 via-purple-900 to-slate-900 flex items-center justify-center p-4">
      <div className="w-full max-w-5xl h-[90vh] bg-slate-800/50 backdrop-blur-xl rounded-3xl shadow-2xl border border-purple-500/20 flex flex-col overflow-hidden">
        
        {/* Header */}
        <div className="bg-gradient-to-r from-purple-600 to-pink-600 p-6 flex items-center gap-4">
          <div className="bg-white/20 p-3 rounded-xl backdrop-blur-sm">
            <Sparkles className="w-8 h-8 text-white" />
          </div>
          <div className="flex-1">
            <h1 className="text-2xl font-bold text-white">Assistant IA</h1>
            <div className="flex items-center gap-4 mt-1">
              <span className="flex items-center gap-1 text-purple-100 text-sm">
                <Globe className="w-4 h-4" />
                Recherche Web
              </span>
              <span className="flex items-center gap-1 text-purple-100 text-sm">
                <Code className="w-4 h-4" />
                Expert Code
              </span>
            </div>
          </div>
        </div>

        {/* Messages */}
        <div className="flex-1 overflow-y-auto p-6 space-y-4">
          {messages.length === 0 && (
            <div className="text-center text-gray-400 mt-20">
              <Sparkles className="w-16 h-16 mx-auto mb-4 text-purple-400" />
              <h2 className="text-2xl font-bold mb-2 text-white">Bonjour ! 👋</h2>
              <p className="mb-6">Je suis ton assistant IA avec accès à la recherche web.</p>
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4 max-w-2xl mx-auto text-left">
                <div className="bg-slate-700/50 p-4 rounded-xl border border-purple-500/20">
                  <Globe className="w-6 h-6 text-purple-400 mb-2" />
                  <p className="text-sm text-gray-300">Recherche des infos actuelles sur le web</p>
                </div>
                <div className="bg-slate-700/50 p-4 rounded-xl border border-purple-500/20">
                  <Code className="w-6 h-6 text-pink-400 mb-2" />
                  <p className="text-sm text-gray-300">Génère du code dans tous les langages</p>
                </div>
              </div>
            </div>
          )}
          
          {messages.map((msg, idx) => (
            <div
              key={idx}
              className={`flex ${msg.role === 'user' ? 'justify-end' : 'justify-start'}`}
            >
              <div
                className={`max-w-[80%] rounded-2xl p-4 ${
                  msg.role === 'user'
                    ? 'bg-gradient-to-r from-purple-600 to-pink-600 text-white'
                    : msg.isSearching
                    ? 'bg-blue-500/20 border border-blue-500/30 text-blue-300'
                    : 'bg-slate-700/50 text-gray-100 border border-slate-600/50'
                }`}
              >
                <div className="whitespace-pre-wrap break-words">
                  {msg.content.split('```').map((part, i) => {
                    if (i % 2 === 1) {
                      const lines = part.split('\n');
                      const lang = lines[0];
                      const code = lines.slice(1).join('\n');
                      return (
                        <pre key={i} className="bg-slate-900 p-4 rounded-lg my-2 overflow-x-auto">
                          <div className="text-xs text-purple-400 mb-2">{lang}</div>
                          <code className="text-sm text-gray-300">{code}</code>
                        </pre>
                      );
                    }
                    return <span key={i}>{part}</span>;
                  })}
                </div>
              </div>
            </div>
          ))}
          
          {loading && (
            <div className="flex justify-start">
              <div className="bg-slate-700/50 rounded-2xl p-4 border border-slate-600/50">
                <Loader2 className="w-6 h-6 text-purple-400 animate-spin" />
              </div>
            </div>
          )}
          
          <div ref={messagesEndRef} />
        </div>

        {/* Input */}
        <div className="p-6 bg-slate-800/80 border-t border-slate-700">
          <div className="flex gap-3">
            <input
              type="text"
              value={input}
              onChange={(e) => setInput(e.target.value)}
              onKeyPress={(e) => e.key === 'Enter' && handleSubmit(e)}
              placeholder="Pose-moi une question ou demande-moi de coder quelque chose..."
              className="flex-1 bg-slate-700 text-white rounded-xl px-6 py-4 focus:outline-none focus:ring-2 focus:ring-purple-500 border border-slate-600"
              disabled={loading}
            />
            <button
              onClick={handleSubmit}
              disabled={loading || !input.trim()}
              className="bg-gradient-to-r from-purple-600 to-pink-600 text-white rounded-xl px-8 py-4 font-semibold hover:from-purple-700 hover:to-pink-700 disabled:opacity-50 disabled:cursor-not-allowed transition-all duration-200 flex items-center gap-2 shadow-lg hover:shadow-xl"
            >
              <Send className="w-5 h-5" />
              Envoyer
            </button>
          </div>
        </div>
      </div>
    </div>
  );
}
