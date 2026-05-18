import React, { useState, useRef, useEffect, useCallback } from 'react';
// As bibliotecas PDF-Lib e PDF.js são carregadas via CDN, então não há necessidade de importá-las aqui.

// Componente principal do aplicativo
export default function App() {
  // --- ESTADOS DO COMPONENTE ---
  const [pages, setPages] = useState([]); // Armazena as páginas dos arquivos carregados
  const [metadata, setMetadata] = useState({ // Armazena os metadados para o nome do arquivo
    dataPagamento: '',
    boletoComprovante: 'BOLETO E COMPROVANTE',
    nomePrestador: '',
    valor: '',
  });
  const [loading, setLoading] = useState(false); // Estado de carregamento para processamento de arquivos e geração de PDF
  const [aiLoading, setAiLoading] = useState(false); // Estado de carregamento para a sugestão da IA
  const [draggedItem, setDraggedItem] = useState(null); // Item sendo arrastado para reordenação
  const [error, setError] = useState(''); // Mensagens de erro para o usuário
  const [scriptsReady, setScriptsReady] = useState(false); // Rastreia se as bibliotecas PDF (CDN) foram carregadas
  const [paymentStatusMessage, setPaymentStatusMessage] = useState(''); // Mensagem de status de pagamento da IA

  // --- REFERÊNCIAS FIREBASE (Placeholder conforme instruções) ---
  const db = useRef(null);
  const auth = useRef(null);
  const userId = useRef(null);
  const isAuthReady = useRef(false);

  // --- EFEITOS (HOOKS) ---

  // Efeito para inicializar o Firebase (executa uma vez)
  useEffect(() => {
    const initializeFirebase = async () => {
      try {
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : null;
        if (firebaseConfig) {
          const { initializeApp } = await import('firebase/app');
          const { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } = await import('firebase/auth');
          const { getFirestore } = await import('firebase/firestore');

          const app = initializeApp(firebaseConfig);
          db.current = getFirestore(app);
          auth.current = getAuth(app);

          onAuthStateChanged(auth.current, async (user) => {
            if (user) {
              userId.current = user.uid;
            } else {
              const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;
              if (initialAuthToken) {
                await signInWithCustomToken(auth.current, initialAuthToken);
              } else {
                await signInAnonymously(auth.current);
              }
              userId.current = auth.current.currentUser?.uid || crypto.randomUUID();
            }
            isAuthReady.current = true;
            console.log("Firebase inicializado e estado de autenticação pronto. ID do Usuário:", userId.current);
          });
        } else {
          console.warn("Configuração do Firebase não encontrada. Executando sem Firebase.");
          isAuthReady.current = true;
          userId.current = crypto.randomUUID();
        }
      } catch (e) {
        console.error("Falha ao inicializar o Firebase:", e);
        isAuthReady.current = true;
        userId.current = crypto.randomUUID();
      }
    };
    initializeFirebase();
  }, []);

  // Efeito para carregar as bibliotecas PDF do CDN (executa uma vez)
  useEffect(() => {
    const loadPdfLibraries = () => {
      let pdfLibLoaded = false;
      let pdfjsMainLoaded = false;
      let pdfjsWorkerLoaded = false;

      const checkAllLoaded = () => {
        if (pdfLibLoaded && pdfjsMainLoaded && pdfjsWorkerLoaded) {
          if (window.pdfjsLib) {
            window.pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
          }
          setScriptsReady(true);
          console.log("Todas as bibliotecas PDF carregadas com sucesso.");
        }
      };

      // Carrega pdf-lib
      const pdfLibScript = document.createElement('script');
      pdfLibScript.src = 'https://unpkg.com/pdf-lib/dist/pdf-lib.min.js';
      pdfLibScript.onload = () => { pdfLibLoaded = true; checkAllLoaded(); };
      pdfLibScript.onerror = () => { setError('Erro ao carregar bibliotecas essenciais. Tente recarregar a página.'); };
      document.head.appendChild(pdfLibScript);

      // Carrega o script principal do pdfjs-dist
      const pdfjsMainScript = document.createElement('script');
      pdfjsMainScript.src = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.min.js';
      pdfjsMainScript.onload = () => { pdfjsMainLoaded = true; checkAllLoaded(); };
      pdfjsMainScript.onerror = () => { setError('Erro ao carregar bibliotecas essenciais. Tente recarregar a página.'); };
      document.head.appendChild(pdfjsMainScript);

      // Carrega o script worker do pdfjs-dist
      const pdfjsWorkerScript = document.createElement('script');
      pdfjsWorkerScript.src = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.11.174/pdf.worker.min.js';
      pdfjsWorkerScript.onload = () => { pdfjsWorkerLoaded = true; checkAllLoaded(); };
      pdfjsWorkerScript.onerror = () => { setError('Erro ao carregar bibliotecas essenciais. Tente recarregar a página.'); };
      document.head.appendChild(pdfjsWorkerScript);
    };

    loadPdfLibraries();

    // Função de limpeza para remover os scripts ao desmontar o componente
    return () => {
      const scripts = document.querySelectorAll('script[src*="pdf-lib"], script[src*="pdf.js"]');
      scripts.forEach(script => script.remove());
    };
  }, []);

  // Desestrutura os globais das bibliotecas PDF após o carregamento
  const PDFDocument = scriptsReady ? window.PDFLib.PDFDocument : null;
  const rgb = scriptsReady ? window.PDFLib.rgb : null;
  const StandardFonts = scriptsReady ? window.PDFLib.StandardFonts : null;
  const pdfjsLib = scriptsReady ? window.pdfjsLib : null;

  // --- FUNÇÕES DE MANIPULAÇÃO DE EVENTOS ---

  /**
   * Processa os arquivos selecionados pelo usuário, extraindo texto de PDFs "comprovante".
   * @param {React.ChangeEvent<HTMLInputElement>} event - O evento de mudança do input de arquivo.
   */
  const handleFileChange = async (event) => {
    if (!scriptsReady) {
      setError('Aguarde o carregamento das bibliotecas PDF.');
      return;
    }
    const files = Array.from(event.target.files);
    setLoading(true);
    setError('');

    const processFile = async (file) => {
      const id = `${file.name}-${Date.now()}-${Math.random()}`;
      if (file.type.startsWith('image/')) {
        return new Promise((resolve, reject) => {
          const reader = new FileReader();
          reader.onload = (e) => resolve({ id, type: 'image', file, dataUrl: e.target.result, textContent: null });
          reader.onerror = () => reject(`Erro ao ler o arquivo de imagem: ${file.name}`);
          reader.readAsDataURL(file);
        });
      } else if (file.type === 'application/pdf') {
        try {
          const arrayBuffer = await file.arrayBuffer();
          const pdf = await pdfjsLib.getDocument({ data: arrayBuffer }).promise;
          const tempPages = [];
          
          let fullTextContent = null;
          if (file.name.toLowerCase().includes('comprovante')) {
              let textSnippets = [];
              for (let i = 1; i <= pdf.numPages; i++) {
                  const page = await pdf.getPage(i);
                  const textContent = await page.getTextContent();
                  textSnippets.push(...textContent.items.map(item => item.str));
              }
              fullTextContent = textSnippets.join(' ');
          }

          for (let i = 1; i <= pdf.numPages; i++) {
            tempPages.push({ 
              id: `${id}-page-${i}`, 
              type: 'pdf', 
              file, 
              pdfDoc: pdf, 
              pageNumber: i,
              textContent: fullTextContent
            });
          }
          return tempPages;
        } catch (err) {
          console.error(`Erro ao processar PDF ${file.name}:`, err);
          throw new Error(`Erro ao processar o arquivo PDF: ${file.name}. Verifique se é um PDF válido.`);
        }
      } else {
        throw new Error(`Tipo de arquivo não suportado: ${file.name}. Por favor, selecione imagens (JPG, PNG) ou PDFs.`);
      }
    };

    try {
      const allProcessedPages = await Promise.all(files.map(processFile));
      setPages((prev) => [...prev, ...allProcessedPages.flat()].sort((a, b) => a.id.localeCompare(b.id)));
    } catch (err) {
      setError(err.message || 'Ocorreu um erro ao processar os arquivos.');
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  /**
   * Remove uma página da lista de pré-visualização.
   * @param {string} idToDelete - O ID da página a ser removida.
   */
  const handleDeletePage = (idToDelete) => {
    setPages(currentPages => currentPages.filter(page => page.id !== idToDelete));
  };

  const handleDragStart = (e, index) => {
    setDraggedItem(pages[index]);
    e.dataTransfer.effectAllowed = 'move';
    e.dataTransfer.setData('text/plain', index);
  };

  const handleDragOver = (e, index) => {
    e.preventDefault();
    if (!draggedItem || draggedItem.id === pages[index].id) return;
    const newPages = [...pages];
    const draggedIndex = newPages.findIndex(p => p.id === draggedItem.id);
    const [removed] = newPages.splice(draggedIndex, 1);
    newPages.splice(index, 0, removed);
    setPages(newPages);
  };

  const handleDrop = (e) => {
    e.preventDefault();
    setDraggedItem(null);
  };

  const handleDragEnd = () => {
    setDraggedItem(null);
  };

  const handleMetadataChange = (e) => {
    const { name, value } = e.target;
    setMetadata((prev) => ({ ...prev, [name]: value }));
  };

  // --- FUNÇÕES PRINCIPAIS ---

  const renderPdfPage = useCallback(async (canvas, pdfDoc, pageNumber) => {
    if (!pdfDoc || !canvas || !pdfjsLib) return;
    try {
        const page = await pdfDoc.getPage(pageNumber);
        const viewport = page.getViewport({ scale: 1.0 });
        const context = canvas.getContext('2d');
        canvas.height = viewport.height;
        canvas.width = viewport.width;
        const renderContext = { canvasContext: context, viewport: viewport };
        await page.render(renderContext).promise;
    } catch(err) {
        console.error("Erro ao renderizar página do PDF:", err);
    }
  }, [pdfjsLib]);

  const suggestMetadataWithAI = async () => {
    if (pages.length === 0) {
      setError('Por favor, adicione arquivos para que a IA possa sugerir detalhes.');
      return;
    }
    setAiLoading(true);
    setError('');
    setPaymentStatusMessage('');

    const fileNames = pages.map(p => p.file.name).join(', ');
    
    const receiptText = pages
        .filter(p => p.textContent)
        .map(p => p.textContent)
        .filter((text, index, self) => self.indexOf(text) === index)
        .join('\n\n---\n\n');

    let prompt = `Analise os nomes de arquivos e o conteúdo de texto abaixo para extrair metadados.`;
    prompt += `\n\nNomes dos arquivos: ${fileNames}`;

    if (receiptText) {
        prompt += `\n\nTexto extraído de arquivos de comprovante (priorize este texto para encontrar a data de pagamento): \n"""${receiptText}"""`;
    }

    prompt += `\n\nSua tarefa é extrair: 'dataPagamento' (no formato DD.MM.YY, usando pontos, ex: 05.12.25. Extraia do texto do comprovante se disponível), 'valor' (adicione o prefixo 'R$ ' e utilize SEMPRE pontos ao invés de vírgulas tanto para separar milhares quanto decimais, ex: R$ 1.313.07), e 'nomePrestador' (o nome de quem recebeu o valor). Além disso, determine o 'statusPagamento' ('pago', 'agendado', ou 'a verificar'). Responda APENAS com o objeto JSON. Se um valor não for encontrado, retorne uma string vazia.`;

    const payload = {
      contents: [{ role: "user", parts: [{ text: prompt }] }],
      generationConfig: {
        responseMimeType: "application/json",
        responseSchema: {
          type: "OBJECT",
          properties: {
            "dataPagamento": { "type": "STRING" },
            "valor": { "type": "STRING" },
            "nomePrestador": { "type": "STRING" },
            "statusPagamento": { "type": "STRING" }
          },
          "propertyOrdering": ["dataPagamento", "valor", "nomePrestador", "statusPagamento"]
        }
      }
    };

    const apiKey = "";
    const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;
    
    let retries = 0;
    const maxRetries = 5;
    const baseDelay = 1000;

    while (retries < maxRetries) {
      try {
        const response = await fetch(apiUrl, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        });

        if (!response.ok) {
          if (response.status === 429) {
            const delay = baseDelay * Math.pow(2, retries) + Math.random() * 1000;
            await new Promise(res => setTimeout(res, delay));
            retries++;
            continue;
          }
          throw new Error(`Erro na API: ${response.status} ${response.statusText}`);
        }

        const result = await response.json();
        if (result.candidates?.[0]?.content?.parts?.[0]) {
          const parsedJson = JSON.parse(result.candidates[0].content.parts[0].text);
          setMetadata(prev => ({
            ...prev,
            dataPagamento: parsedJson.dataPagamento || '',
            valor: parsedJson.valor || '',
            nomePrestador: parsedJson.nomePrestador || ''
          }));

          const status = parsedJson.statusPagamento?.toLowerCase();
          if (status && (status.includes('agendado') || status.includes('a verificar') || status.includes('pendente'))) {
            setPaymentStatusMessage('Atenção: O status do pagamento não é "pago". Verifique o comprovante.');
          } else {
            setPaymentStatusMessage('');
          }
        } else {
          setError('Não foi possível obter sugestões da IA. Tente novamente.');
        }
        break;
      } catch (err) {
        setError(`Erro ao sugerir detalhes com IA: ${err.message}`);
        console.error(err);
        break;
      } finally {
        setAiLoading(false);
      }
    }
  };

  const generateAndSavePdf = async () => {
    if (!scriptsReady || !PDFDocument) {
      setError('Aguarde o carregamento das bibliotecas PDF.');
      return;
    }
    if (pages.length === 0) {
      setError('Por favor, adicione arquivos para gerar o PDF.');
      return;
    }
    setLoading(true);
    setError('');

    try {
      const pdfDoc = await PDFDocument.create();
      
      for (const pageData of pages) {
        if (pageData.type === 'image') {
          const imgBytes = await fetch(pageData.dataUrl).then(res => res.arrayBuffer());
          let embeddedImage;
          if (pageData.file.type === 'image/jpeg') {
            embeddedImage = await pdfDoc.embedJpg(imgBytes);
          } else if (pageData.file.type === 'image/png') {
            embeddedImage = await pdfDoc.embedPng(imgBytes);
          } else {
            continue;
          }
          const page = pdfDoc.addPage();
          const { width, height } = page.getSize();
          const dims = embeddedImage.scaleToFit(width - 50, height - 50);
          page.drawImage(embeddedImage, {
            x: (width - dims.width) / 2,
            y: (height - dims.height) / 2,
            width: dims.width,
            height: dims.height,
          });
        } else if (pageData.type === 'pdf') {
          const originalPdfBytes = await pageData.file.arrayBuffer();
          const originalPdfDoc = await PDFDocument.load(originalPdfBytes);
          const [copiedPage] = await pdfDoc.copyPages(originalPdfDoc, [pageData.pageNumber - 1]);
          pdfDoc.addPage(copiedPage);
        }
      }

      const pdfBytes = await pdfDoc.save();
      const { dataPagamento, boletoComprovante, nomePrestador, valor } = metadata;

      // Utiliza a data fornecida pela IA (DD.MM.YY) mantendo apenas números e pontos
      const formattedDate = dataPagamento 
        ? dataPagamento.replace(/[^0-9.]/g, '') 
        : 'SemData';

      const safeBoletoComprovante = boletoComprovante.replace(/[^a-zA-Z0-9]/g, '_') || 'Documento';
      const safeNomePrestador = nomePrestador.replace(/[^a-zA-Z0-9\s]/g, '') || 'SemPrestador';
      
      // Utiliza o valor formatado pela IA mantendo "R$", números, pontos e espaços
      const safeValor = valor ? valor.replace(/[^R$0-9.\s]/g, '').trim() : 'SemValor';

      const filename = `${formattedDate} ${safeBoletoComprovante} ${safeNomePrestador} ${safeValor}.pdf`;

      const blob = new Blob([pdfBytes], { type: 'application/pdf' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = filename;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      URL.revokeObjectURL(link.href);

    } catch (err) {
      setError('Erro ao gerar o PDF. Verifique os arquivos e tente novamente.');
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  // --- COMPONENTES DE RENDERIZAÇÃO ---

  const PagePreview = ({ page, index, onDelete }) => {
    const canvasRef = useRef(null);

    useEffect(() => {
      if (scriptsReady && page.type === 'pdf' && page.pdfDoc && canvasRef.current) {
        renderPdfPage(canvasRef.current, page.pdfDoc, page.pageNumber);
      }
    }, [page, renderPdfPage, scriptsReady]);

    return (
      <div
        className={`relative group p-2 border border-gray-300 rounded-lg shadow-md bg-white flex flex-col items-center justify-center cursor-grab ${draggedItem?.id === page.id ? 'opacity-50' : ''}`}
        draggable
        onDragStart={(e) => handleDragStart(e, index)}
        onDragOver={(e) => handleDragOver(e, index)}
        onDrop={handleDrop}
        onDragEnd={handleDragEnd}
      >
        <button
          onClick={() => onDelete(page.id)}
          className="absolute top-1 right-1 z-10 p-1 bg-red-600 text-white rounded-full opacity-0 group-hover:opacity-100 transition-opacity duration-200 hover:bg-red-700 focus:outline-none focus:ring-2 focus:ring-red-500"
          aria-label="Remover página"
        >
          <svg xmlns="http://www.w3.org/2000/svg" className="h-4 w-4" viewBox="0 0 20 20" fill="currentColor">
            <path fillRule="evenodd" d="M4.293 4.293a1 1 0 011.414 0L10 8.586l4.293-4.293a1 1 0 111.414 1.414L11.414 10l4.293 4.293a1 1 0 01-1.414 1.414L10 11.414l-4.293 4.293a1 1 0 01-1.414-1.414L8.586 10 4.293 5.707a1 1 0 010-1.414z" clipRule="evenodd" />
          </svg>
        </button>
        <span className="absolute top-1 left-2 text-xs font-semibold text-gray-500">{index + 1}</span>
        {page.type === 'image' && (
          <img src={page.dataUrl} alt={`Pré-visualização ${index + 1}`} className="max-w-full h-40 object-contain rounded-md" />
        )}
        {page.type === 'pdf' && (
          <canvas ref={canvasRef} className="max-w-full h-40 object-contain rounded-md border border-gray-200"></canvas>
        )}
        <p className="mt-2 text-sm text-center text-gray-700 truncate w-full px-1" title={page.file.name}>{page.file.name} (Pág. {page.type === 'pdf' ? page.pageNumber : '1'})</p>
      </div>
    );
  };

  // --- RENDERIZAÇÃO PRINCIPAL ---

  if (!scriptsReady) {
    return (
      <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 flex items-center justify-center">
        <p className="text-xl text-blue-700 font-semibold flex items-center">
          <svg className="animate-spin -ml-1 mr-3 h-6 w-6 text-blue-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle><path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg>
          Carregando bibliotecas PDF...
        </p>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-4 sm:p-6 lg:p-8 font-sans text-gray-800">
      <div className="max-w-6xl mx-auto bg-white rounded-3xl shadow-xl overflow-hidden">
        <header className="bg-gradient-to-r from-blue-600 to-purple-700 p-6 text-white text-center rounded-t-3xl">
          <h1 className="text-3xl sm:text-4xl font-extrabold tracking-tight">Unir PDFs e Documentos</h1>
          <p className="mt-2 text-lg opacity-90">Junte, organize e salve seus arquivos em um único PDF.</p>
        </header>

        <main className="p-6 sm:p-8 grid grid-cols-1 lg:grid-cols-3 gap-8">
          <section className="lg:col-span-1 bg-gray-50 p-6 rounded-2xl shadow-inner">
            <h2 className="text-2xl font-bold text-blue-700 mb-5">1. Adicionar Arquivos</h2>
            <div className="mb-6">
              <label htmlFor="file-upload" className="block text-sm font-medium text-gray-700 mb-2">Selecione Imagens (.jpg, .png) ou PDFs</label>
              <input
                id="file-upload" type="file" multiple
                accept="image/jpeg,image/png,application/pdf"
                onChange={handleFileChange}
                className="block w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100 cursor-pointer"
              />
              {loading && <p className="mt-3 text-blue-600 text-sm flex items-center"><svg className="animate-spin -ml-1 mr-2 h-5 w-5 text-blue-500" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle><path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg> Processando arquivos...</p>}
              {error && <p className="mt-3 text-red-600 text-sm">{error}</p>}
            </div>

            <h2 className="text-2xl font-bold text-blue-700 mb-5 mt-8">2. Detalhes do Documento</h2>
            <div className="space-y-4">
              <div>
                <label htmlFor="dataPagamento" className="block text-sm font-medium text-gray-700">Data de Pagamento</label>
                <input type="text" id="dataPagamento" name="dataPagamento" value={metadata.dataPagamento} onChange={handleMetadataChange} placeholder="Ex: 05.12.25" className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 p-2"/>
              </div>
              <div>
                <label htmlFor="boletoComprovante" className="block text-sm font-medium text-gray-700">Tipo (Boleto, Comprovante)</label>
                <input type="text" id="boletoComprovante" name="boletoComprovante" value={metadata.boletoComprovante} onChange={handleMetadataChange} placeholder="Ex: Boleto Luz" className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 p-2"/>
              </div>
              <div>
                <label htmlFor="nomePrestador" className="block text-sm font-medium text-gray-700">Nome do Prestador/Beneficiário</label>
                <input type="text" id="nomePrestador" name="nomePrestador" value={metadata.nomePrestador} onChange={handleMetadataChange} placeholder="Ex: Eletrobras" className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 p-2"/>
              </div>
              <div>
                <label htmlFor="valor" className="block text-sm font-medium text-gray-700">Valor</label>
                <input type="text" id="valor" name="valor" value={metadata.valor} onChange={handleMetadataChange} placeholder="Ex: R$ 123.45" className="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 p-2"/>
              </div>
              <button onClick={suggestMetadataWithAI} disabled={pages.length === 0 || aiLoading} className="mt-4 w-full bg-purple-600 hover:bg-purple-700 text-white font-bold py-2 px-4 rounded-full shadow-md transition duration-300 ease-in-out transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center">
                {aiLoading ? (<> <svg className="animate-spin -ml-1 mr-2 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle><path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg> Sugerindo... </>) : (<> ✨ Sugerir Detalhes com IA </>)}
              </button>
              {paymentStatusMessage && (
                <div className="mt-4 p-3 rounded-lg bg-yellow-100 border border-yellow-400 text-yellow-800 text-sm font-medium flex items-center">
                  <svg className="h-5 w-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"></path></svg>
                  {paymentStatusMessage}
                </div>
              )}
            </div>
          </section>

          <section className="lg:col-span-2 bg-gray-50 p-6 rounded-2xl shadow-inner">
            <h2 className="text-2xl font-bold text-blue-700 mb-5">3. Pré-visualização e Organização</h2>
            <p className="text-gray-600 mb-4">Arraste para reordenar ou passe o mouse para excluir.</p>
            <div className="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-6 gap-4 p-4 min-h-[300px] max-h-[600px] overflow-y-auto border border-dashed border-gray-300 rounded-lg bg-white" onDragOver={(e) => e.preventDefault()} onDrop={handleDrop}>
              {pages.length === 0 ? (
                <p className="col-span-full text-center text-gray-500 py-10">Nenhuma página adicionada ainda.</p>
              ) : (
                pages.map((page, index) => (
                  <PagePreview key={page.id} page={page} index={index} onDelete={handleDeletePage} />
                ))
              )}
            </div>
            <div className="mt-8 flex justify-center">
              <button onClick={generateAndSavePdf} disabled={pages.length === 0 || loading || !scriptsReady} className="bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-8 rounded-full shadow-lg transition duration-300 ease-in-out transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed flex items-center">
                {loading ? (<> <svg className="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24"><circle className="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" strokeWidth="4"></circle><path className="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path></svg> Gerando PDF... </>) : (<> <svg className="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M8 7H5a2 2 0 00-2 2v9a2 2 0 002 2h14a2 2 0 002-2V9a2 2 0 00-2-2h-3m-1 4l-3 3m0 0l-3-3m3 3V4"></path></svg> Salvar PDF Final </>)}
              </button>
            </div>
          </section>
        </main>

        <footer className="bg-gray-800 p-4 text-center text-gray-300 text-sm rounded-b-3xl">
          <p>&copy; {new Date().getFullYear()} Desenvolvido por Carlos Henrique com React e PDF-Lib.</p>
        </footer>
      </div>
    </div>
  );
}
