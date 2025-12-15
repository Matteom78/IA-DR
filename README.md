import React, { useState, useRef, useEffect } from 'react';
import { Send, Code, Globe, Sparkles, Loader2, Copy, Check } from 'lucide-react';

export default function AICodeAssistant() {
  const [messages, setMessages] = useState([]);
  const [input, setInput] = useState('');
  const [loading, setLoading] = useState(false);
  const [copied, setCopied] = useState(null);
  const messagesEndRef = useRef(null);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  const copyToClipboard = (text, id) => {
    navigator.clipboard.writeText(text);
    setCopied(id);
    setTimeout(() => setCopied(null), 2000);
  };

  const handleSubmit = async () => {
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
          system: `Tu es un assistant IA expert en programmation. Tu as accès à la recherche web pour trouver les informations les plus récentes. 

Quand tu écris du code :
- Ajoute des commentaires clairs en français
- Utilise les meilleures pratiques
- Propose du code complet et fonctionnel
- Explique tes choix techniques

Quand tu as besoin d'informations récentes, utilise la recherche web.`,
          messages: [
            ...messages.map(m => ({ role: m.role, content: m.content })),
            { role: "user", content: userMessage }
          ],
          tools: [
            {
              "type": "web_search_20250305",
              "name": "web_search"
            }
          ]
        }),
      });

      const data = await response.json();
      
      let assistantMessage = '';
      let usedWebSearch = false;

      if (data.content) {
        for (const block of data.content) {
          if (block.type === 'text') {
            assistantMessage += block.text;
          } else if (block.type === 'tool_use' && block.name === 'web_search') {
            usedWebSearch = true;
          }
        }
      }

      setMessages(prev => [...prev, { 
        role: 'assistant', 
        content: assistantMessage,
        usedWebSearch 
      }]);
    } catch (error) {
      setMessages(prev => [...prev, { 
        role: 'assistant', 
        content: '❌ Erreur lors de la communication avec l\'IA. Veuillez réessayer.' 
      }]);
    } finally {
      setLoading(false);
    }
  };

  const renderMessage = (message, index) => {
    const isUser = message.role === 'user';
    const hasCode = message.content.includes('```');
    
    let parts = [];
    if (hasCode) {
      const segments = message.content.split(/(```[\s\S]*?```)/g);
      parts = segments.map((segment, i) => {
        if (segment.startsWith('```')) {
          const code = segment.replace(/```[\w]*\n?/, '').replace(/```$/, '');
          const lang = segment.match(/```(\w+)/)?.[1] || '';
          return { type: 'code', content: code, lang, id: `${index}-${i}` };
        }
        return { type: 'text', content: segment };
      });
    } else {
      parts = [{ type: 'text', content: message.content }];
    }

    return (
      <div key={index} className={`flex ${isUser ? 'justify-end' : 'justify-start'} mb-4`}>
        <div className={`max-w-[80%] ${isUser ? 'order-2' : 'order-1'}`}>
          <div className={`flex items-start gap-2 ${isUser ? 'flex-row-reverse' : 'flex-row'}`}>
            <div className={`w-8 h-8 rounded-full flex items-center justify-center ${
              isUser ? 'bg-gradient-to-br from-blue-500 to-purple-600' : 'bg-gradient-to-br from-emerald-500 to-teal-600'
            }`}>
              {isUser ? '👤' : '🤖'}
            </div>
            <div className="flex-1">
              {message.usedWebSearch && (
                <div className="flex items-center gap-1 text-xs text-emerald-400 mb-2">
                  <Globe className="w-3 h-3" />
                  <span>Recherche web effectuée</span>
                </div>
              )}
              <div className={`rounded-2xl p-4 ${
                isUser 
                  ? 'bg-gradient-to-br from-blue-600 to-purple-600 text-white' 
                  : 'bg-gray-800 text-gray-100'
              }`}>
                {parts.map((part, i) => {
                  if (part.type === 'code') {
                    return (
                      <div key={i} className="my-3 bg-gray-900 rounded-lg overflow-hidden">
                        <div className="flex items-center justify-between px-3 py-2 bg-gray-950 border-b border-gray-700">
                          <span className="text-xs text-gray-400">{part.lang || 'code'}</span>
                          <button
                            onClick={() => copyToClipboard(part.content, part.id)}
                            className="text-gray-400 hover:text-white transition-colors"
                          >
                            {copied === part.id ? (
                              <Check className="w-4 h-4 text-emerald-400" />
                            ) : (
                              <Copy className="w-4 h-4" />
                            )}
                          </button>
                        </div>
                        <pre className="p-3 overflow-x-auto text-sm">
                          <code className="text-gray-100">{part.content}</code>
                        </pre>
                      </div>
                    );
                  }
                  return <div key={i} className="whitespace-pre-wrap">{part.content}</div>;
                })}
              </div>
            </div>
          </div>
        </div>
      </div>
    );
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-900 via-gray-800 to-gray-900 text-white p-4">
      <div className="max-w-5xl mx-auto h-screen flex flex-col">
        {/* Header */}
        <div className="bg-gray-800/50 backdrop-blur-lg rounded-2xl p-6 mb-4 border border-gray-700 shadow-2xl">
          <div className="flex items-center gap-3 mb-2">
            <div className="w-12 h-12 rounded-xl bg-gradient-to-br from-emerald-500 to-teal-600 flex items-center justify-center">
              <Sparkles className="w-6 h-6" />
            </div>
            <div>
              <h1 className="text-3xl font-bold bg-gradient-to-r from-emerald-400 to-teal-400 bg-clip-text text-transparent">
                IA Assistant de Code
              </h1>
              <p className="text-gray-400 text-sm">Powered by Claude Sonnet 4 + Recherche Web</p>
            </div>
          </div>
          <div className="flex gap-4 mt-4">
            <div className="flex items-center gap-2 bg-gray-700/50 px-3 py-2 rounded-lg">
              <Code className="w-4 h-4 text-blue-400" />
              <span className="text-sm text-gray-300">Expert en Code</span>
            </div>
            <div className="flex items-center gap-2 bg-gray-700/50 px-3 py-2 rounded-lg">
              <Globe className="w-4 h-4 text-emerald-400" />
              <span className="text-sm text-gray-300">Recherche Web</span>
            </div>
          </div>
        </div>

        {/* Messages */}
        <div className="flex-1 overflow-y-auto bg-gray-800/30 backdrop-blur-sm rounded-2xl p-6 mb-4 border border-gray-700">
          {messages.length === 0 ? (
            <div className="h-full flex flex-col items-center justify-center text-gray-500">
              <Sparkles className="w-16 h-16 mb-4 text-emerald-500" />
              <h2 className="text-xl font-semibold mb-2">Commencez une conversation</h2>
              <p className="text-center max-w-md">
                Posez-moi des questions sur le code, demandez-moi de créer des applications,
                ou cherchez des informations récentes sur le web !
              </p>
            </div>
          ) : (
            <>
              {messages.map(renderMessage)}
              {loading && (
                <div className="flex justify-start mb-4">
                  <div className="flex items-center gap-2 bg-gray-800 rounded-2xl p-4">
                    <Loader2 className="w-5 h-5 animate-spin text-emerald-400" />
                    <span className="text-gray-300">L'IA réfléchit...</span>
                  </div>
                </div>
              )}
              <div ref={messagesEndRef} />
            </>
          )}
        </div>

        {/* Input */}
        <div className="bg-gray-800/50 backdrop-blur-lg rounded-2xl p-4 border border-gray-700 shadow-2xl">
          <div className="flex gap-3">
            <input
              type="text"
              value={input}
              onChange={(e) => setInput(e.target.value)}
              onKeyPress={(e) => e.key === 'Enter' && handleSubmit()}
              placeholder="Demandez-moi de coder quelque chose ou de chercher des infos..."
              className="flex-1 bg-gray-700/50 text-white px-4 py-3 rounded-xl border border-gray-600 focus:border-emerald-500 focus:outline-none focus:ring-2 focus:ring-emerald-500/50 transition-all"
              disabled={loading}
            />
            <button
              onClick={handleSubmit}
              disabled={loading || !input.trim()}
              className="bg-gradient-to-r from-emerald-500 to-teal-600 hover:from-emerald-600 hover:to-teal-700 disabled:from-gray-600 disabled:to-gray-700 px-6 py-3 rounded-xl font-semibold transition-all transform hover:scale-105 disabled:scale-100 disabled:cursor-not-allowed flex items-center gap-2 shadow-lg"
            >
              {loading ? (
                <Loader2 className="w-5 h-5 animate-spin" />
              ) : (
                <Send className="w-5 h-5" />
              )}
            </button>
          </div>
        </div>
      </div>
    </div>
  );
}
